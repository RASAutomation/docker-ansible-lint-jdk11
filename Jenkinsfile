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

def uuid = UUID.randomUUID().toString()
def label = "build-ansible-lint-jdk11-${uuid}"
def testPodLabel = "test-ansible-lint-jdk11-${uuid}"
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

            stage('Checkout SCM for Build') {
                timeout(time: 3, unit: 'MINUTES') {
                    checkout([
                        $class: 'GitSCM',
                        branches: scm.branches,
                        doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                        extensions: [[$class: 'CleanBeforeCheckout']],
                        userRemoteConfigs: scm.userRemoteConfigs
                    ])
                }

                // Build Control flow
                dockerRepository = "287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11"
                if ( env.CHANGE_ID != null ) {
                    // Building for Pull Request
                    dockerTag = "PR-${env.CHANGE_ID}"
                    kanikoDestinations = """
                        --destination=${dockerRepository}:${dockerTag}
                    """
                } else if ( env.TAG_NAME != null ) {
                    // Building for Git Tag
                    dockerTag = "${env.TAG_NAME}"
                    kanikoDestinations = """
                        --destination=${dockerRepository}:${dockerTag}
                    """
                } else if ( env.BRANCH_NAME == 'master') {
                    // Building for `master` branch
                    dockerTag = "latest"
                    kanikoDestinations = """
                        --destination=${dockerRepository}:${dockerTag}
                    """
                } else {
                    // Building for arbitrary branch
                    dockerTag = "${env.BRANCH_NAME}-latest"
                    def shortCommitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
                    kanikoDestinations = """
                        --destination=${dockerRepository}:${dockerTag} \
                        --destination=${dockerRepository}:${env.BRANCH_NAME}-${shortCommitHash}
                    """
                }
            }

            stage('Docker Build docker-ansible-lint-jdk11') {
                container(name: 'kaniko', shell: '/busybox/sh'){
                    sh """
                    #!/busybox/sh
                    /kaniko/executor \
                        -f `pwd`/Dockerfile \
                        -c `pwd` \
                        ${kanikoDestinations}
                    """
                }
            }

        } //dir(workdir) {
    } // node(label) {
} //podTemplate(...) {

podTemplate(
    label: testPodLabel,
    containers: [containerTemplate(name: 'ansible-lint-jdk11', image: "287908807331.dkr.ecr.us-east-2.amazonaws.com/ansible-lint-jdk11:${dockerTag}", ttyEnabled: true, command: 'cat')]
) {
    node(testPodLabel) {
        dir(workdir) {

            stage('Test ansible-lint version') {
                container(name: 'ansible-lint-jdk11', shell: '/bin/bash') {
                    def ansibleLintVersion = sh(script: 'ansible-lint --version', returnStdout: true) =~ /\d{1,3}\.\d{1,3}\.\d{1,3}/

                    if (compareVersions(v1: '4.1.0', v2: ansibleLintVersion[0], failIfEmpty: true) != 0) {
                        error("Expected ansible-lint version 4.1.0. Got ${ansibleLintVersion[0]}")
                    }
                }
            }

            stage('Test openJDK version') {
                container(name: 'ansible-lint-jdk11', shell: '/bin/bash') {
                    def jdkVersion = sh(script: 'java -version 2>&1', returnStdout: true) =~ /\d{1,3}\.\d{1,3}\.\d{1,3}/

                    if (compareVersions(v1: '11.0.0', v2: jdkVersion[0], failIfEmpty: true) == 1) {
                        error("Expected jdk version  >= 11.0.0; Got ${jdkVersion[0]}")
                    }
                }
            }

            stage('Test SonarQube Analysis') {
                timeout(time: 3, unit: 'MINUTES') {
                    checkout([
                        $class: 'GitSCM',
                        branches: scm.branches,
                        doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                        extensions: [[$class: 'CleanBeforeCheckout']],
                        userRemoteConfigs: scm.userRemoteConfigs
                    ])
                }
                def scannerHome = tool 'SonarScanner 4.3';
                container(name: 'ansible-lint-jdk11'){
                    withSonarQubeEnv() {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ansible-lint-jdk11 -Dsonar.projectBaseDir=${workdir}"
                    }
                }
            }

        } //dir(workdir) {
    } // node(testPodLabel) {
} //podTemplate(...) {
