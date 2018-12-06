#!/usr/bin/env groovy

def nodes
def versions

node {
    dir('/home/pencillr/workspace') {
        nodes = sh (script: 'sh list_nodes.sh', returnStdout: true).trim()
    }
}

pipeline {
agent any
    parameters {
            choice(name: 'Invoke_Parameters', choices:"Yes\nNo", description: "Do you whish to do a dry run to grab parameters?" )
            choice(name: 'Nodes', choices:"${nodes}", description: "")
    }
    stages {
        stage("parameterizing") {
            steps {
                script {
                    if ("${params.Invoke_Parameters}" == "Yes") {
                        currentBuild.result = 'ABORTED'
                        error('DRY RUN COMPLETED. JOB PARAMETERIZED.')
                    }
                }
            }
        }
        stage("choose version") {
            steps {
                script {
                    def version_collection
                    def chosen_node = "${params.Nodes}"
                    dir('/home/pencillr/workspace') {
                         version_collection = sh (script: "sh list_versions.sh $chosen_node", returnStdout: true).trim()
                    }
                        versions = input message: 'Choose testload version!', ok: 'SET', parameters: [choice(name: 'TESTLOAD_VERSION', choices: "${version_collection}", description: '')]


        stage("build") {
            steps {
                script {
                    build(job: "builder-job",
                        parameters:
                        [string(name: 'Nodes', value: "${params.Nodes}"),
                        string(name: 'Versions', value: "${versions}"),
                        ])
                }
            }
        }
    }
}
