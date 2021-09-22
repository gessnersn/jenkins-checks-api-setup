node("master"){
  stage("Checkout"){
    checkout scm
  }

  stage("discoverGitReferenceBuild"){
    discoverGitReferenceBuild defaultBranch: "master"
  }

  stage("Publish succeeding check"){
    publishChecks name: "successful check",  
                  // pre-condition for "conclusion" is that "status" is "COMPLETED" (which is the default)
                  conclusion: "SUCCESS",  // Values: NONE, ACTION_REQUIRED, SKIPPED, CANCELED, TIME_OUT, FAILURE, NEUTRAL, SUCCESS
                  status: "COMPLETED",  // can be "QUEUED", "IN_PROGRESS", or "COMPLETED". By default, "COMPLETED"
                  title: "Success title", 
                  summary: "# Success check summary",  // will be displayed before "text" (Markdown support)
                  text: "This successful check is published through the pipeline script",  // the details about the results (Markdown support)
                  detailsURL: env.BUILD_URL  // visible in Checks tab at the bottom to link to further details, away from GitHub
  }

  stage("Publish failing check"){
    publishChecks name: "failing check", 
                  conclusion: "FAILURE",
                  title: "Failing title", 
                  summary: "# Failing check summary",
                  text: "Failure happens...",
                  detailsURL: env.BUILD_URL
  }
  
  stage("Publish failing check"){
    publishChecks name: "canceled check", 
                  conclusion: "CANCELED",
                  title: "Canceled title", 
                  summary: "# Canceled check summary",
                  text: "Canceled ...",
                  detailsURL: env.BUILD_URL
  }
  
  stage("Publish neutral check"){
    publishChecks name: "neutral check", 
                  conclusion: "NEUTRAL",
                  title: "Neutral title", 
                  summary: "# Neutral check summary",
                  text: "Neutral ...",
                  detailsURL: env.BUILD_URL
  }

  stage("Publish via Warnings NG Plugin"){
    recordIssues(
      tools: [
        issues(
          name: 'Warnings', 
          pattern: 'warnings.json'
        )
      ]
    )
  }

    
  stage("Publish JUnit"){
    sh "touch junit.xml"
    junit 'junit.xml'
  }
  
  stage("Publish Coverage"){
    publishCoverage adapters: [
      coberturaAdapter(
        path: 'coverage.xml', 
        thresholds: [
          [
            thresholdTarget: 'Conditional', 
            unhealthyThreshold: 80.0, 
            unstableThreshold: 30.0
          ]
        ]
      )
    ],
    calculateDiffForChangeRequests: true,
    sourceFileResolver: sourceFiles('STORE_ALL_BUILD')
  }
}
