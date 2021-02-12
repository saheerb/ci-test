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

//currentBuild.description = getNextJobUrl(env.JOB_NAME)
env.STANDALONE=true

stage ("build") {
    build job: 
        'mbed-os-ci-tfm-build', 
        parameters: [
            string(name: 'targets_toolchains_build', value: params['targets_toolchains_build']),
            string(name: 'mbed_os_topic', value: params['mbed_os_topic']),
            string(name: 'mbed_os_fork', value: params['mbed_os_fork']),
            string(name: 'upstream_build_number', value: env.BUILD_NUMBER),
            string(name: 's3_upload_name', value: env.JOB_NAME),
            string(name: 's3_base_path', value: s3.getBasePath()),
            booleanParam(name: 'standalone', value: true)
        ], 
        wait: true, 
        propagate: true
}

stage ("test") {
    build job: 
        'mbed-os-ci-tfm-test', 
        parameters: [
            string(name: 'targets_toolchains_test', value: params['targets_toolchains_test']),
            string(name: 'mbed_os_topic', value: params['mbed_os_topic']),
            string(name: 'mbed_os_fork', value: params['mbed_os_fork']),
            string(name: 'upstream_build_number', value: env.BUILD_NUMBER),
            string(name: 's3_upload_name', value: env.JOB_NAME),
            string(name: 's3_base_path', value: s3.getBasePath())
        ], 
        wait: true, 
        propagate: true
}

