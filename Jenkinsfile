node{
  stage ('Build') {
    git url: 'https://github.com/markramach/spring-boot-prototype-docker'
    withMaven(maven: 'maven') {
      sh "mvn clean install"
    } 
  }
}
