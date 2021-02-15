/**
 * mbed-os-ci-dev test main job jenkinsfile. Prepares mbed-os-ci branches for testing & executes selected tests
 * Used to test mbed-os-ci scripts https://github.com/ARMmbed/mbed-os-ci
 * Pipeline defined in https://mbed-os-development.mbedcloudtesting.com/job/mbed-os-ci-dev_main/
 */

properties([
        disableConcurrentBuilds(),
        parameters([
            string(name: 'targets_toolchains_build',
                defaultValue:  params.targets_toolchains_build ?: "['ARM_MUSCA_S1':['GNUARM'], 'ARM_MUSCA_B1':['GNUARM']]",
                description: 'Map of target and toolchains to build'),
            string(name: 'targets_toolchains_test',
                defaultValue: params.targets_toolchains_test ?: "['ARM_MUSCA_S1':['GNUARM']]",
                description: 'Map of target and toolchains to test. This must be subset of targets_toolchains_build'),
            string(name: 'mbed_os_fork',
                defaultValue: params.mbed_os_fork ?: 'ARMmbed/mbed-os',
                description: 'mbed-os fork'),
            string(name: 'mbed_os_topic',
                defaultValue: params.mbed_os_topic ?: 'master',
                description: 'mbed-os branch'),
        ])
])
echo "params in main job are ${params}"

/**
 * This need extra permissions from Jenkins maintainer
 * @param jobName
 * @return
 */
def getNextJobUrl(jobName){
    def buildNumber = Jenkins.instance.getItem(jobName).lastBuild.number + 1
    return "<a href=\"${env.JENKINS_URL}job/${jobName}/${buildNumber}/\">Job: ${jobName}</a> <br \\>"
}

def mbedOSTopic = params.mbed_os_topic
def mbedOSFork = params.mbed_os_fork
def gitHubBranchId = github.getBranchId(mbedOSTopic)
def upstreamBuildNumber = env.BUILD_NUMBER
def s3UploadName = env.JOB_NAME
def s3BasePath = s3.getBasePath()
def s3Bucket = s3.getDefaultBucket()

//currentBuild.description = getNextJobUrl(env.JOB_NAME)
/*
stage("setup") {
    cipipeline.cinode(label: "all-in-one-build-slave", timeout: 5400) {
            def s3SourcePath = "${s3BasePath}/sources/${gitHubBranchId}/${upstreamBuildNumber}/${s3UploadName}.tar.gz"

            dir("mbed-os-tf-m-regression-tests"){
                checkout scm
                sh "ls"
                sh "git clone https://github.com/${mbedOSFork}.git -b ${mbedOSTopic}"
            }

            sh "tar -czf sources.tar.gz mbed-os-tf-m-regression-tests --exclude-vcs"

            // upload source
            s3.upload("sources.tar.gz", s3SourcePath, s3Bucket, "eu-west-1")
    }
}
*/
/*
stage ("build") {
    build job: 
        'mbed-os-ci-tfm-build', 
        parameters: [
            string(name: 'targets_toolchains', value: params['targets_toolchains_build']),
            string(name: 'mbed_os_topic', value: params['mbed_os_topic']),
            string(name: 'mbed_os_fork', value: params['mbed_os_fork']),
            string(name: 'upstream_build_number', value: upstreamBuildNumber),
            string(name: 's3_upload_name', value: s3UploadName),
            string(name: 's3_base_path', value: s3BasePath),
            string(name: 'pr_head_sha', value: github.getOriginalPrHeadSha()),
            //booleanParam(name: 'standalone', value: true)
        ], 
        wait: true, 
        propagate: true
}

stage ("test") {
    build job: 
        'mbed-os-ci-tfm-test', 
        parameters: [
            string(name: 'targets_toolchains', value: params['targets_toolchains_test']),
            string(name: 'mbed_os_topic', value: params['mbed_os_topic']),
            string(name: 'mbed_os_fork', value: params['mbed_os_fork']),
            string(name: 'upstream_build_number', value: upstreamBuildNumber),
            string(name: 's3_upload_name', value: s3UploadName),
            string(name: 's3_base_path', value: s3BasePath),
        ], 
        wait: true, 
        propagate: true
}
*/


mbed.run_job(
        subBuildsPostfix: "-standalone",
        mbed_os_fork: params['mbed_os_fork'],
        mbed_os_topic: params['mbed_os_topic'],
        targets_toolchains_build: params['targets_toolchains_build'],
        targets_toolchains_test: params['targets_toolchains_test'],
        setProperties: false,
        checkout: null
)


