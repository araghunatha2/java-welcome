node   ('maven'){
  
  // Mark the code checkout 'stage'....
  stage 'Checkout'
  // Get some code from a GitHub repository
 
  git url: 'https://github.com/araghunatha2/java-welcome.git'
  // Clean any locally modified files and ensure we are actually on origin/master
  // as a failed release could leave the local workspace ahead of origin/master
  sh "git clean -f && git reset --hard origin/master"
  def mvnHome = tool 'M3'
  // we want to pick up the version from the pom
  def pom = readMavenPom file: 'pom.xml'
  def version = pom.version.replace("-SNAPSHOT", ".${currentBuild.number}")
  def mvnCmd = "mvn -s ${mvnHome}/conf/settings.xml"
  sh "${mvnCmd} clean install -DskipTests=true"
  // Mark the code build 'stage'....
  
   stage 'Deploy DEV' 
               sh "rm -rf oc-build && mkdir -p oc-build/deployments"
               sh "cp target/jersey-mysql.war oc-build/deployments/ROOT.war"
               sh "oc project conti"
               // clean up. keep the image stream
               sh "oc delete bc,dc,svc,route -l app=newtasks2 -n conti"
               // create build. override the exit code since it complains about exising imagestream
               sh "oc new-build --name=newtasks2 --image-stream=jboss-webserver30-tomcat8-openshift --binary=true --labels=app=newtasks2 -n conti || true"
               // build image
               sh "oc start-build newtasks2 --from-dir=oc-build --wait=true -n conti"
               // deploy image
               sh "oc new-app newtasks2:latest -n conti"
               sh "oc expose svc/newtasks2 -n conti"
      
  
 // stage 'Build'
  // Run the maven build this is a release that keeps the development version 
  // unchanged and uses Jenkins to provide the version number uniqueness
  
  //sh "${mvnHome}/bin/mvn -DreleaseVersion=${version} -DdevelopmentVersion=${pom.version} -DpushChanges=false -DlocalCheckout=true -DpreparationGoals=initialize release:prepare release:perform -B" 
  // Now we have a step to decide if we should publish to production 
  // (we just use a simple publish step here)
  }
