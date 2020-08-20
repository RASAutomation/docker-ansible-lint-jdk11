#!/usr/bin/env groovy

/**
 * This pipeline will build and deploy a Docker image with Kaniko
 * https://github.com/GoogleContainerTools/kaniko
 * without needing a Docker host
 *
 * You need to create a jenkins-docker-cfg secret with your docker config
 * as described in
 * https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-in-the-cluster-that-holds-your-authorization-token
 */

def label = "build-ansible-lint-jdk11-${UUID.randomUUID().toString()}"
def home = "/home/jenkins/agent"
def workspace = "${home}/workspace/build-docker-ansible-lint-jdk11"
def workdir = "${workspace}/src/github.com/rasautomation/docker-ansible-lint-jdk11/"

podTemplate (
    label: label,
    yaml:
"""
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
    tty: true
    volumeMounts:
      - name: docker-config
        mountPath: /kaniko/.docker/
    resources:
      limits:
        ephemeral-storage: 5Gi
      requests:
        ephemeral-storage: 2Gi
  volumes:
    - name: docker-config
      configMap:
        name: docker-config
"""
) {
    node(label) {
        dir(workdir) {

            stage('Checkout SCM') {
                timeout(time: 3, unit: 'MINUTES') {
                    checkout([
                        $class: 'GitSCM',
                        branches: scm.branches,
                        doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                        extensions: [[$class: 'CleanBeforeCheckout']],
                        userRemoteConfigs: scm.userRemoteConfigs
                    ])
                }
            }

            stage('Docker Build docker-ansible-lint-jdk11') {
                if ( env.CHANGE_ID != null ) {
                    // Building for a Pull Request
                    echo "Building for PR-${env.CHANGE_ID}: ${env.CHANGE_NAME}"
                    container(name: 'kaniko', shell: '/busybox/sh'){
                        sh """
                        #!/busybox/sh
                        /kaniko/executor \
                            -f `pwd`/Dockerfile \
                            -c `pwd` \
                            --destination=287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:PR-${env.CHANGE_ID}
                        """
                    }
                } else if ( env.TAG_NAME != null ) {
                    // Building for a GitHub TAG / Release
                    echo "Building for git tag: ${env.TAG_NAME}"
                    container(name: 'kaniko', shell: '/busybox/sh'){
                        sh """
                        #!/busybox/sh
                        /kaniko/executor \
                            -f `pwd`/Dockerfile \
                            -c `pwd` \
                            --destination=287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:${env.TAG_NAME}
                        """
                    }
                } else if ( env.BRANCH_NAME == 'master') {
                    // Building for origin/master branch
                    echo "Building for origin/master branch"
                    container(name: 'kaniko', shell: '/busybox/sh'){
                        sh """
                        #!/busybox/sh
                        /kaniko/executor \
                            -f `pwd`/Dockerfile \
                            -c `pwd` \
                            --destination=287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:latest
                        """
                    }
                } else {
                    // Building for arbitrary branch on origin
                    echo "Building for origin/${env.BRANCH_NAME} branch"
                    def shortCommitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
                    container(name: 'kaniko', shell: '/busybox/sh'){
                        sh """
                        #!/busybox/sh
                        /kaniko/executor \
                            -f `pwd`/Dockerfile \
                            -c `pwd` \
                            --destination=287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:${env.BRANCH_NAME}-latest \
                            --destination=287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:${env.BRANCH_NAME}-${shortCommitHash}
                        """
                    }
                }
            }

            stage('Test docker-ansible-lint-jdk11 container') {
                def testPodLabel = "test-ansible-lint-jdk11-${UUID.randomUUID().toString()}"
                podTemplate(
                    label: testPodLabel,
                    containers: [containerTemplate(name: 'golang', image: 'golang:1.8.0', ttyEnabled: true, command: 'cat')]
                ) {
                    node(testPodLabel) {
                        echo "FOOBAR"
                    }
                }
            }

        } //dir(workdir) {
    } // node(label) {
} //podTemplate(...) {
