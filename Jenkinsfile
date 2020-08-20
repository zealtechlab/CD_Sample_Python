pipeline { 
    agent any
    options {
        ansiColor('xterm')
        skipStagesAfterUnstable()
    }

    stages {
        // CloneCode stage is commented as the repo is already cloned by the Jenkins pipe
        stage("CloneCode") {
            steps {
                script {
                    sh 'printenv'
                    // git %GIT_URL%;
                }
            }
        }
        stage ('Create Inventory') {
           steps {
               script{
                //    println "\n\n-- Running on machine: " + "hostname -i".execute().text
                   sh '''
                   ip=$(hostname -i | cut -d ' ' -f 1 | sed "s/(([0-9]{1,3}.){1,3}).[0-9]*$//g" | sed "s/[0-9]*$/1/")
                   echo "[targets]" > hosts.ini
                   echo $ip " ansible_python_interpreter=/usr/bin/python3 ansible_connection=ssh ansible_user=prabhakaran ansible_ssh_private_key_file=/var/jenkins_home/.ssh/id_rsa" >> hosts.ini
                   cat hosts.ini
                   '''
               }
           }
        //    post {
        //         always {
        //             perfReport filterRegex: '', sourceDataFiles: '**/*.xml'
        //         }
        //         success {
        //             echo '${env.BUILD_NUMBER} ${env.BUILD_URL} Inventory Creation SUCCESS'
        //         }
        //         failure {
        //             echo '${env.BUILD_NUMBER} ${env.BUILD_URL} Inventory Creation FAILED'
        //         }
        //     }
        }
        stage ('Deploy') {
           steps {
            //    ansiblePlaybook {
            //             playbook('ansible/microk8sbloggerappplaybook.yml')
            //             inventoryPath('hosts.ini')
                        // ansibleName('1.9.4')
                        // tags('one,two')
                        // credentialsId('credsid')
                        // become(true)
                        // becomeUser("user")
                        // extraVars {
                        //     extraVar("key1", "value1", false)
                        //     extraVar("key2", "value2", true)
                    // }
               script{sh "ansible-playbook -i hosts.ini ansible/microk8sbloggerappplaybook.yml -e ansible_python_interpreter=/usr/bin/python3"}
           }
        //    post {
        //         always {
        //             perfReport filterRegex: '', sourceDataFiles: '**/*.xml'
        //         }
        //         success {
        //             echo '${env.BUILD_NUMBER} ${env.BUILD_URL} DEPLOY SUCCESSFUL'
        //         }
        //         failure {
        //             echo '${env.BUILD_NUMBER} ${env.BUILD_URL} DEPLOY FAILED'
        //         }
        //     }
        }
    }
    
    post {
        always {
            echo 'JENKINS PIPELINE - $env.BUILD_URL, ${env.BUILD_NUMBER}'
        }
        notBuilt {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE NOT BUILT, STOPPED at STAGE - ${env.STAGE_NAME}'
        }
        success {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE SUCCESSFUL'
        }
        failure {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE FAILED at Stage - ${env.STAGE_NAME}'
        }
        unstable {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE WAS MARKED AS UNSTABLE'
        }
        changed {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE STATUS HAS CHANGED SINCE LAST EXECUTION'
        }
        aborted {
            echo '${env.BUILD_NUMBER} ${env.BUILD_URL} JENKINS PIPELINE ABORTED'
        }
    }
}