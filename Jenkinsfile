pipeline {
    agent {label "lsldev && linux"}
    stages {
        stage("Download rcn-ee key") {
            steps {
                sh "./create_debian_packages download"
                stash name: "aptkeydeb", includes: 'debs/*/*.deb'
            }
        }
        stage("create debian packages") {
            agent {label "mhadev && linux"}
            steps {
                sh "./create_debian_packages apt-sources"
                stash name: "aptsrcdeb", includes: 'debs/*/*.deb'
            }
        }
        stage("debian packages for apt") {
            agent {label "aptly"}
            steps {
                // receive all created deb packages from build
                unstash "aptkeydeb"
                unstash "aptsrcdeb"
                archiveArtifacts "debs/*/*.deb"

                // Copies the new debs to the stash of existing debs,
                sh "BRANCH_NAME=master make SUPPLY_DIR=debs/ PROJECT=rcn-ee-repo"

                build job:         "/hoertech-aptly/master",
                      quietPeriod: 300,
                      wait:        false
            }
        }
    }

    // Email notification on failed build taken from
    // https://jenkins.io/doc/pipeline/tour/post/
    // multiple recipients are comma-separated:
    // https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#-mail-%20mail
    post {
        failure {
            mail to: 't.herzke@hoertech.de',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
