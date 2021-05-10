
pipeline {
  agent any

  stages {
    stage('Build') {
      when {
        anyOf {
          branch 'master'
        }
      }
      steps {
        sh '''#!/bin/bash
                        freePort(){
                             while true
                             do
                                random_port=$(( ((RANDOM<<15)|RANDOM) % 49152 + 10000 ))
                                status="$(nc -z 127.0.0.1 $random_port < /dev/null &>/dev/null; echo $?)"
                                if [ "${status}" != "0" ]; then
                                    echo "$random_port";
                                    break;
                                fi
                            done
                        }
                        freePortJetty=$(freePort)
                        myJettyName='ainosoft-site-jetty'
                        docker run --name $myJettyName -d -p $freePortJetty:8080 --mount type=bind,source=$(pwd)/,destination=/var/lib/jetty/webapps/ jetty  
        '''                
      }
    }
  }

  post {
    always {
      cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenSuccess: true, cleanWhenUnstable: true, deleteDirs: true)
    }
  }
}
