node('windows') {
	def jobDir = pwd()
	def solutionFilePath = "\"${jobDir}\\mRemoteV1.sln\""
	def vsToolsDir = "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\Common7\\Tools"
	def vsExtensionsDir = "C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\Common7\\IDE\\CommonExtensions\\Microsoft\\TestWindow"
	
	stage 'Checkout Branch'
	def gitUrl = "https://github.com/mRemoteNG/mRemoteNG.git"
	def branchName = GetBranchName()
	echo "BranchName: ${branchName}"
	git([url: gitUrl, branch: branchName])

	stage 'Restore NuGet Packages'
	def nugetPath = "C:\\nuget.exe"
	bat "${nugetPath} restore ${solutionFilePath}"

	stage 'Build mRemoteNG (Normal)'
	bat "\"${vsToolsDir}\\VsDevCmd.bat\" && msbuild.exe /nologo /p:Platform=x86 \"${jobDir}\\mRemoteV1.sln\""

	stage 'Build mRemoteNG (Portable)'
	bat "\"${vsToolsDir}\\VsDevCmd.bat\" && msbuild.exe /nologo /p:Configuration=\"Debug Portable\";Platform=x86 \"${jobDir}\\mRemoteV1.sln\""

	stage 'Run Unit Tests'
	def nunitTestAdapterPath = "C:\\Users\\Administrator\\AppData\\Local\\Microsoft\\VisualStudio\\14.0\\Extensions"
	bat "\"${vsToolsDir}\\VsDevCmd.bat\" && VSTest.Console.exe /TestAdapterPath:${nunitTestAdapterPath} \"mRemoteNGTests\\bin\\debug\\mRemoteNGTests.dll\""
}
def GetBranchName() {
	def jobDir = pwd()
	echo "JobDir: ${jobDir}"
	def patternToUse = GetPatternToMatchBranchNameFromDirectory()
	echo "PatternToUse: ${patternToUse}"
	java.util.regex.Matcher matcher = jobDir =~ patternToUse
	def rawBranchName = matcher ? matcher[0][1] : null
	echo "Raw branch name: ${rawBranchName}"
	def modifiedBranchName = ConvertHtmlSlashToSlashUsedByGit(rawBranchName)
	echo "Modified branch name: ${modifiedBranchName}"
	return modifiedBranchName
}
def GetPatternToMatchBranchNameFromDirectory() {
	def patternToUse = ""
	def folderSeparator = ""
	def branchNamePattern = "[a-zA-Z0-9-_%]*"
	def jenkinsJobInfoTag = "@*[0-9]*"
	echo "isUnix: ${isUnix()}"
	if (isUnix()) {
		folderSeparator = "/"
	} else {
		folderSeparator =  "\\\\"
	}
	patternToUse = "${folderSeparator}(${branchNamePattern})(${jenkinsJobInfoTag})\$"
	return patternToUse
}
def ConvertHtmlSlashToSlashUsedByGit(stringToChange)
{
	def modifiedString = stringToChange.replaceAll(/%2F/, "/")
	return modifiedString
}
