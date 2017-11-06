pipeline {
  agent any
  stages {
    stage('Clean Workspace') {
      steps {
        echo 'cleaning workspace'
      }
    }
    stage('Git Clone') {
      parallel {
        stage('Dataplatform') {
          steps {
            dir(path: 'Dataplatform') {
              git(url: 'https://github.com/riversandtechnologies/dataplatform', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('RSConnect') {
          steps {
            dir(path: 'rsconnect') {
              git(url: 'https://github.com/riversandtechnologies/rsconnect.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('RSDAM') {
          steps {
            dir(path: 'rsdam') {
              git(url: 'https://github.com/riversandtechnologies/rsdam.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('UI') {
          steps {
            dir(path: 'ui') {
              git(url: 'https://github.com/riversandtechnologies/ui-platform.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
        stage('devops') {
          steps {
            dir(path: 'devops') {
              git(url: 'https://github.com/riversandtechnologies/dataplatform-devops.git', branch: 'master', credentialsId: '12b41655-3811-40bc-8a87-35ce9b56c3fb')
            }
            
          }
        }
      }
    }
    stage('Build dataplatform') {
      steps {
        dir(path: 'Dataplatform/dataplatform-solution') {
          sh '''# Set version number
#/usr/share/apache-maven-3.5.0/bin/mvn versions:set -DnewVersion=1.1.$BUILD_NUMBER'''
          sh '''# compile project
#/usr/share/apache-maven-3.5.0/bin/mvn -T 4 compile package install -DskipTests'''
          sh '''# Run Tests
                #/usr/share/apache-maven-3.5.0/bin/mvn test -fae
                #/usr/share/apache-maven-3.5.0/bin/mvn -T 6 cobertura:cobertura -Dcobertura.report.format=xml'''
        }
        
      }
    }
    stage('Build RSConnet') {
      steps {
        dir(path: 'rsconnect/rsconnect-solution') {
          sh '''# Set version number
#/usr/share/apache-maven-3.5.0/bin/mvn versions:set -DnewVersion=1.1.$BUILD_NUMBER'''
          sh '''# compile project
#/usr/share/apache-maven-3.5.0/bin/mvn -T 4 compile package install -DskipTests'''
          sh '''# Run Tests
#/usr/share/apache-maven-3.5.0/bin/mvn test -fae
#/usr/share/apache-maven-3.5.0/bin/mvn -T 6 cobertura:cobertura -Dcobertura.report.format=xml'''
        }
        
      }
    }
    stage('Build RSDAM and UI') {
      parallel {
        stage('Build RSDAM') {
          steps {
            dir(path: 'rsdam/rsdam-solution') {
              sh '''# Set version number
#/usr/share/apache-maven-3.5.0/bin/mvn versions:set -DnewVersion=1.1.$BUILD_NUMBER'''
              sh '''# compile project
#/usr/share/apache-maven-3.5.0/bin/mvn -T 4 compile package install -DskipTests'''
              sh '''# Run Tests
#/usr/share/apache-maven-3.5.0/bin/mvn test -fae
#/usr/share/apache-maven-3.5.0/bin/mvn -T 6 cobertura:cobertura -Dcobertura.report.format=xml'''
            }
            
          }
        }
        stage('Build UI') {
          steps {
            dir(path: 'ui') {
              sh '''# clean
#bower cache clean'''
              sh '''# install
#npm install
#bower install
#npm i -g gulp-cli'''
              sh '''# compile
#npm run compile'''
            }
            
          }
        }
      }
    }
    stage('Cleanup Docker Folder') {
      steps {
        dir(path: 'docker') {
          sh '''#Cleanup and creating drop path

rm -rf *'''
        }
        
      }
    }
    stage('Create Directory structure') {
      steps {
        dir(path: 'docker') {
          sh '''mkdir -p config/services
mkdir -p config/messages
mkdir templates
mkdir rsconnect
mkdir jar
mkdir nodejs
mkdir input_files
mkdir nginx'''
        }
        
      }
    }
    stage('Copy Files') {
      parallel {
        stage('RDP') {
          steps {
            sh '''rdp_workspace=$WORKSPACE/Dataplatform/dataplatform-solution
droppath=$WORKSPACE/docker

cd "$rdp_workspace"

droppath_temp="$droppath/jar/"

declare -a rdp_projects=(\'frameworksvc-entitymanagesvc-topology\' \'frameworksvc-eventmanagesvc-topology\' \'frameworksvc-entitygovernsvc-topology\' \'platformsvc-apihostsvc\' \'frameworksvc-entityfamilygovernsvc-topology\' \'frameworksvc-entityfamilymanagesvc-topology\' \'frameworksvc-entitymanagemodelsvc-topology\' \'frameworksvc-notificationmanagesvc-topology\' \'frameworksvc-requestmanagesvc-topology\' \'frameworksvc-binaryobjectmanagesvc-topology\' \'frameworksvc-configurationmanagesvc-topology\' \'frameworksvc-erroreventmanagesvc-topology\' \'frameworksvc-externaleventmanagesvc-topology\' \'enginesvc-commonlib\' \'frameworksvc-entitygraphcomputesvc-topology\' \'frameworksvc-binarystreamobjectmanagesvc-topology\' \'appsvc-entitysvc-topology\' \'frameworksvc-eventreportsvc-topology\' \'frameworksvc-schedulemanagesvc-topology\');

jarfile=\'\'
sourcefilepath=\'\'

for i in "${rdp_projects[@]}"
do
	sourcefilepath="$rdp_workspace/$i/target/"
	cd "$sourcefilepath"

	jarfile=$(ls -t | grep "$i" |  head -n1 )
	sourcefilepath="$sourcefilepath$jarfile"	
   
	if [ -z "$jarfile" ] && [ -f "$" ];
	then
		echo "JAR file not found for : $i"
	else
		cp "$sourcefilepath" "$droppath_temp"
	fi
done

#Copy config
tempdir="$rdp_workspace/platformsvc-configmanager/src/main/resources"
droppath_temp="$droppath/config/"


cp "$tempdir/prod_dataplatformpodconfig_template.json" "$droppath_temp"
cp "$tempdir/dev_dataplatformpodconfig_template.json" "$droppath_temp"
cp "$tempdir/qa_dataplatformpodconfig_template.json" "$droppath_temp"
cp "$tempdir/dataplatformpodconfig_template.json" "$droppath_temp"
cp "$tempdir/tenantserviceconfig_template.json" "$droppath_temp"
cp "$tempdir/log4j2.xml" "$droppath_temp"
cp "$tempdir/logstash.conf" "$droppath_temp"
cp "$tempdir/messageconfig.json" "$droppath_temp"

#ES Mappings
tempdir="$rdp_workspace/platformsvc-configmanager/src/main/resources/mappings"
droppath_temp="$droppath/templates/"

for f in $tempdir/*.json; 
do
    cp $f "$droppath_temp"
done

#Nodejs
tempdir="$rdp_workspace/platformsvc-authenticationsvc/src"
droppath_temp="$droppath/nodejs/"
cp -R $tempdir/keys $droppath_temp
mkdir -p "$droppath_temp/files/"
cp -R $tempdir/config "$droppath_temp/files/"
cp -R $tempdir/server "$droppath_temp/files/"
cp $tempdir/app.js "$droppath_temp/files/"
cp $tempdir/package.json "$droppath_temp/files/"

#templates for tenant onboarding
tempdir="$rdp_workspace/platformsvc-configmanager/src/main/resources"
droppath_temp="$droppath/input_files/"

for f in $tempdir/*.json; 
do
	cp $f "$droppath_temp"
done

#Nginx conf
cp $rdp_workspace/platformsvc-serverhostsvc/nginx_template.conf $droppath/nginx/nginx.conf
'''
          }
        }
        stage('RSConnect') {
          steps {
            sh '''rsconnect_workspace=$WORKSPACE/rsconnect/rsconnect-solution/
droppath=$WORKSPACE/docker
droppath_temp="$droppath/jar/"

cd "$rsconnect_workspace"

for d in */ ; do
	tempdir=$rsconnect_workspace/$d/target/

	if [ -d "$tempdir" ];
	then
		cd $tempdir

		prj=${d////}
		jarfile=$(ls -t | grep "$prj" |  head -n1 )

		if [ -f "$jarfile" ];
		then
			cp "$jarfile" "$droppath_temp"
		fi
	fi

    cd "$rsconnect_workspace"
done

#rsconnect driver
cd "$rsconnect_workspace"

droppath_temp="$droppath/rsconnect/"
cp rsconnect-driver/src/main/resources/rsconnect-driver.sh $droppath_temp/rsconnect-driver.sh

#COP config
droppath_temp="$droppath/config/services/"
cp rsconnect-core/src/main/resources/com/riversand/rsconnect/common/rsconnect/driver/services/rsconnectservicesconfig_template.json $droppath_temp/rsconnectservicesconfig.json

#RSConnet Inbound config
droppath_temp="$droppath/config/services/"
cp rsconnect-entityinboundsvc/src/main/resources/rsinboundserviceconfig_template.json $droppath_temp/rsinboundserviceconfig.json

#Message config
cp rsconnect-core/src/main/resources/com/riversand/rsconnect/common/rsconnect/driver/messages/rsconnectmessageconfig.json $droppath/config/messages/

#Message config - Inbound service 
cp rsconnect-entityinboundsvc/src/main/resources/rsinboundmessageconfig.json $droppath/config/messages/

#templates for tenant onboarding
tempdir="$rsconnect_workspace/rsconnect-core/src/main/resources/com/riversand/rsconnect/common/rsconnect/driver/services/"
droppath_temp="$droppath/input_files/"

for f in $tempdir/*.json; 
do
	cp $f "$droppath_temp"
done

# Template for tenant onboarding - Inbound service 
cp rsconnect-entityinboundsvc/src/main/resources/rsconnectbpftopologiesconfig.json "$droppath_temp"
'''
          }
        }
        stage('DAM') {
          steps {
            sh '''rsdam_workspace=$WORKSPACE/rsdam/rsdam-solution/
droppath=$WORKSPACE/docker

cd "$rsdam_workspace"

droppath_temp="$droppath/jar/"
for d in */ ; do
	tempdir=$rsdam_workspace/$d/target/

	if [ -d "$tempdir" ];
	then
		cd $tempdir

		prj=${d////}
		jarfile=$(ls -t | grep "$prj" |  head -n1 )

		if [ -f "$jarfile" ];
		then
			cp "$jarfile" "$droppath_temp"
		fi
	fi

    cd "$rsdam_workspace"
done

#rsconnect driver
cd "$rsdam_workspace"

#RSDAM config
droppath_temp="$droppath/config/services/"
cp rsdam-core/src/main/resources/com/riversand/rsdam/common/services/prod_rsdamserviceconfig_template.json $droppath_temp/rsdamserviceconfig.json

#Message config
cp rsdam-core/src/main/resources/com/riversand/rsdam/common/services/rsdammessageconfig.json $droppath/config/messages/

#thumbnails
mkdir $droppath/assetThumbnails
cp rsdam-core/src/main/resources/com/riversand/rsdam/common/services/assetThumbnails/* $droppath/assetThumbnails


#templates for tenant onboarding
tempdir="$rsdam_workspace/rsdam-core/src/main/resources/com/riversand/rsdam/common/services/"
droppath_temp="$droppath/input_files/"

for f in $tempdir/*.json; 
do
	echo $f
	cp $f "$droppath_temp"
done
'''
          }
        }
        stage('UI') {
          steps {
            dir(path: 'ui') {
              sh '''pwd

cp -R node_modules/ build/unbundled/ui-platform/

cd build/unbundled/

tar -czf ui-platform-1.1.$BUILD_NUMBER.tar.gz ui-platform

#rm -rf /var/lib/rs/docker/package/ui-platform*

cp ui-platform-1.1.$BUILD_NUMBER.tar.gz $WORKSPACE/docker'''
            }
            
          }
        }
      }
    }
    stage('Create Docker Containers') {
      steps {
        dir(path: 'devops/violet/Docker/Swarm/pre-built/') {
          sh '''find . -type f -name \'*.sh\' -exec sed -i -e \'s/\\r$//\' {} \\;
chmod +x build.sh
SOURCE_DIR=$WORKSPACE/docker
BUILD_NUMBER="1.1.$BUILD_NUMBER"

#./build.sh $SOURCE_DIR $BUILD_NUMBER $BUILD_NUMBER $BUILD_NUMBER $BUILD_NUMBER dev'''
        }
        
        sh '''echo "Generating rdp deploy package"

packageversion=$(date +%m%d%y.%H%M)
TAG_SUFFIX=dev

echo $packageversion${TAG_SUFFIX} > rdp_deploy_version.txt

cd $WORKSPACE/devops/violet/Docker/Swarm/30-rdp-deploy
find . -type f -name \'*.sh\' -exec sed -i -e \'s/\\r$//\' {} \\;
#./build.sh $WORKSPACE/docker  $packageversion${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX}


#Tenant Onboarding
echo "Building Tenant On-boarding"
cd $WORKSPACE/devops/violet/Docker/Swarm/35-tenant-deploy
#./build.sh $WORKSPACE/docker $packageversion${TAG_SUFFIX}

#Code base upgrade
echo "Building code base upgrade"
cd $WORKSPACE/devops/violet/Docker/Swarm/46-upgrade
chmod +x ./build.sh
find . -type f -name \'*.sh\' -exec sed -i -e \'s/\\r$//\' {} \\;
#./build.sh $WORKSPACE/docker $packageversion${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX} ${BUILD_NUMBER}${TAG_SUFFIX}'''
      }
    }
    stage('Deploy') {
      steps {
        sh '''deploy_version=$(cat rdp_deploy_version.txt)

ssh -i $KEY_FOLDER_PATH$KEY_FILE_NAME $REMOTE_SERVER "./auto-deploy-dev1.sh $deploy_version'''
      }
    }
  }
  environment {
    KEY_FOLDER_PATH = '/home/bigdataadmin/keys/'
    KEY_FILE_NAME = 'riversand-east-1.pem'
    REMOTE_SERVER = 'ubuntu@54.210.14.231'
  }
}