pipeline {
    agent any

    stages 
	{
		stage( 'Checkout Source' ) 
		{
		//Checkout source code from GIT
		  steps
		  {
			// workaround to get GIT plugin environment Variables, we need to collect the checkout command output, which is a map that contains them
			// https://issues.jenkins-ci.org/browse/JENKINS-35230
			  script
			  {
				scm_map = checkout scm
				GIT_BRANCH = scm_map['GIT_BRANCH']
				// get just the branch name minus the remote only splitting on first
				// match in case the rest of the branch has more '/' chars.
				GIT_BRANCH_NAME = GIT_BRANCH.split('/',2)[1]				
			  }				
				 bat "echo 'Checkout of GIT branch: ${GIT_BRANCH}'"
			     bat "echo 'GIT_BRANCH_NAME: ${GIT_BRANCH_NAME}'"
			}
		}//End Checkout Source   
		
		stage( 'Build' ) 
		{
		//Build source code
		  steps
		  {
			bat "\"${tool 'MSBuild 14'}\" NunitDemo.sln /p:Configuration=Release /p:Platform=\"Any CPU\" /p:ProductVersion=1.0.0.${env.BUILD_NUMBER}"   
			}
		}//End Build source code 
		
		stage( 'Test' ) 
		{
		//Build source code
		  steps
		  {
			bat '''"C:\\Program Files (x86)\\Jenkins\\workspace\\Pipe\\packages\\OpenCover.4.6.519\\tools\\OpenCover.Console.exe" -target:"C:\\Program Files (x86)\\Jenkins\\workspace\\Pipe\\packages\\NUnit.ConsoleRunner.3.7.0\\tools\\nunit3-console.exe" -targetargs:"/work:Pipe\\NunitDemo.Test\\bin\\Debug\\Scripts\\CodeCoverageReport --out:TestResult.txt .\\NunitDemo.Test\\bin\\Release\\NunitDemo.Test.dll"  -output:"Pipe\\NunitDemo.Test\\bin\\Debug\\ScriptsCodeCoverageResult.xml"
				"C:\\Program Files (x86)\\Jenkins\\workspace\\Pipe\\packages\\ReportGenerator.3.1.0\\tools\\ReportGenerator.exe" "-reports:Pipe\\NunitDemo.Test\\bin\\Debug\\Scripts\\CodeCoverageReportCodeCoverageResult.xml" "-targetdir:Pipe\\NunitDemo.Test\\bin\\Debug\\Scripts\\CodeCoverageReport"
				"C:\\Program Files (x86)\\Jenkins\\workspace\\Pipe\\packages\\ReportUnit.1.2.1\\tools\\ReportUnit.exe" "Reporting" "Reporting\\Result"'''
			}
		}
		//End Build source code 
		
		stage ( 'code coverage' )
		{
			steps
			{
				publishHTML([allowMissing: false,
					     alwaysLinkToLastBuild: true, 
					     keepAll: false,
					     reportDir: 'Reporting',
					     reportFiles: 'index.htm',
					     reportName: 'Code Coverage Report',
					     reportTitles: 'Code Coverage Report'])
			}
		}

		
		

		stage( 'Package into zip file' ) 
		{
		//Build source code
		  steps
		  {
			bat '"C:\\Program Files\\7-Zip\\7z.exe" a  -r "DemoNunit.zip" -w NunitDemo.Test\\bin\\Release\\* -mem=AES256'
			}
		}//End Build source code
		stage ( "Sonar" ){                	  
 			 steps
			{
    	    withSonarQubeEnv('SonarQube Scanner 3.0.3.778') {
      bat "{scannerHome}E:/Sonar/sonar-runner-dist-2.4/sonar-runner-2.4/bin/sonar-runner.bat"
		}
			}
	}
	}
}
