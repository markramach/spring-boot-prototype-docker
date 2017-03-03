node{
  stage ('Build') {
    git url: 'https://github.com/markramach/spring-boot-prototype-docker'
    maven(maven: 'maven') {
      sh "mvn clean install"
    } 
  }
}
