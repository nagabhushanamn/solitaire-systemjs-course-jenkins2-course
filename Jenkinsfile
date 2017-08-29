


stage 'CI'
node {
    emailNotify('started')
    
    try{
    //checkout scm
    git branch: 'master', 
        url: 'https://github.com/nagabhushanamn/solitaire-systemjs-course-jenkins2-course'

    // pull dependencies from npm
    // on windows use: bat 'npm install'
    sh 'npm install'

    // stash code & dependencies to expedite subsequent testing
    // and ensure same code & dependencies are used throughout the pipeline
    // stash is a temporary archive
    stash name: 'everything', 
          excludes: 'test-results/**', 
          includes: '**'
    
    // test with PhantomJS for "fast" "generic" results
    // on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
    sh 'npm run test-single-run -- --browsers PhantomJS'
    sh 'npm run test-single-run -- --browsers Chrome'
    sh 'npm run test-single-run -- --browsers Firefox'
    
    // archive karma test results (karma is configured to export junit xml files)
    step([$class: 'JUnitResultArchiver', 
          testResults: 'test-results/**/test-results.xml'])

    emailNotify('success')  

    }catch(err){
        emailNotify('Failed');
        currentBuild.result = 'FAILURE'
    }    
}



def emailNotify(status){
    emailext (
      to: "nag@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
               <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}