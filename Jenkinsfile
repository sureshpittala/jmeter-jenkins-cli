pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { checkout scm}
    }
    stage('JMeter') {
      steps {
        bat '''
        if exist logs rmdir /s /q logs
        if exist html rmdir /s /q html
        mkdir logs
        mkdir html
        mkdir html\\report
        "C:\\jmeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t API.jmx -l logs/results.jtl -e -o html/report        
        -Jjmeter.save.saveservice.output_format=csv ^
        -Jjmeter.save.saveservice.response_code=true ^
        -Jjmeter.save.saveservice.response_message=true ^
        -Jjmeter.save.saveservice.successful=true ^
        -Jjmeter.save.saveservice.label=true ^
        -Jjmeter.save.saveservice.bytes=true ^
        -Jjmeter.save.saveservice.thread_counts=true ^
        -Jjmeter.save.saveservice.latency=true
        '''
      }
    }
    stage('Reports') {
      steps {
        perfReport sourceDataFiles: 'logs/results.jtl'
        publishHTML(
          target: [
            reportDir: 'html/report',
            reportFiles: 'index.html',
            reportName: 'JMeter HTML Report',
            keepAll: true,
            alwaysLinkToLastBuild: true,
            allowMissing: false
            ]
          )
      }
    }
  }
  post {
    always {
      archiveArtifacts 'logs/results.jtl, html/report/**'
    }
  }
}
