


stage 'CI'

node {
    //emailNotify('started')
    
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

    //emailNotify('success')  

    }catch(err){
        //emailNotify('Failed');
        currentBuild.result = 'FAILURE'
    }    
}


// demoing a second agent

// node('mac'){
   
//      // on windows use: bat 'dir'
//     sh 'ls'

//     // on windows use: bat 'del /S /Q *'
//     sh 'rm -rf *'

//     unstash 'everything'

//     // on windows use: bat 'dir'
//     sh 'ls'
// }


parallel chrome: {
    runTests("Chrome")
}, firefox: {
    runTests("Firefox")
}, safari: {
    runTests("Safari")
}


def runTests(browser) {
    node('mac') {
        // on windows use: bat 'del /S /Q *'
        sh 'rm -rf *'
        unstash 'everything'
        // on windows use: bat "npm run test-single-run -- --browsers ${browser}"
        sh "npm run test-single-run -- --browsers ${browser}"
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
    }
}


node {
    emailNotify("Deploy to staging?")
}

input 'Deploy to staging?'



// limit concurrency so we don't perform simultaneous deploys
// and if multiple pipelines are executing, 
// newest is only that will be allowed through, rest will be canceled
stage name: 'Deploy to staging', concurrency: 1
node {
    // write build number to index page so we can see this update
    // on windows use: bat "echo '<h1>${env.BUILD_DISPLAY_NAME}</h1>' >> app/index.html"
    sh "echo '<h1>${env.BUILD_DISPLAY_NAME}</h1>' >> app/index.html"
    
    // deploy to a docker container mapped to port 3000
    // on windows use: bat 'docker-compose up -d --build'
    sh 'docker-compose up -d --build'
    
    emailNotify 'Solitaire Deployed!'
}



def emailNotify(status){
    emailext (
      to: "nag@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
               <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}