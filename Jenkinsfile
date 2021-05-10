
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
                        mkdir -p webapps/ainosoft/
                        cp -r website/* webapps/ainosoft/
                        ls webapps/ainosoft/
                        freePortJetty=$(freePort)
                        myJettyName='ainosoft-site-jetty'
                        docker stop $myJettyName || true
                        docker rm $myJettyName || true
                        docker run --name $myJettyName -d -p $freePortJetty:8080 --mount type=bind,source=webapps/,destination=/var/lib/jetty/webapps jetty  
                        ipAddr="$(ip -o route get to 8.8.8.8 | sed -n 's/.*src \\([0-9.]\\+\\).*/\\1/p')"
                        echo "Staged url - > http://$ipAddr:$freePortJetty/ainosoft"
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
