pipeline {
    agent any

    tools {
        gradle 'gradle-v5.3'
        jdk 'jdk8'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building......'
                sh '''#!/bin/bash -x
                  gradle -v
                  gradle build -x test
                  echo "PATH = ${PATH}"
                  echo "M2_HOME = ${M2_HOME}"
                  echo "JAVA_HOME = ${JAVA_HOME}"
                '''
            }

        }
        stage ('Dev_Deployment') {
            steps{
                sh '''#!/bin/bash -x
                  echo "Who I'm $SHELL"
                  file="manifest.yml"
                  while IFS= read -r line
                  do
                    echo "$line"
                    if [[ "$line" == *"path"* ]]
                    then
                      echo "inside IF"
                      IFS=": "
                      set $line
                      pathValue=$(echo $2)
                      echo "$pathValue"
                      pathDir=$(dirname "$pathValue")
                      echo "$pathDir"
                      oldFileName=$(basename $pathValue)
                      echo "$oldFileName"
                      packaging=${pathValue##*.}
                      echo "$packaging"
                      echo "$(ls $pathDir)"
                      fullfilepath="$(find $pathDir -type f  -name *.$packaging)"
                      echo "$fullfilepath"
                      newFileName=$(basename $fullfilepath)
                      echo "$newFileName"
                      sed -i -e "s/${oldFileName}/${newFileName}/g" "$file"
                    fi
                  done <"$file"
                '''


                pushToCloudFoundry(
                  target: 'https://api.sys.dev.cloudsprint.io',
                  credentialsId: 'pcfcreds',
                  organization: 'FEGO-demo',
                  cloudSpace: 'FEGO-demo',
                  manifestChoice: [manifestFile: 'manifest.yml']
                )
              }
          }
    }
}