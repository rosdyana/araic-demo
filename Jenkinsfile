pipeline {
    agent {
        node {
            label 'agent001'
        }
    }
    stages {
        stage("Restore npm packages") {
            steps {
                // Writes lock-file to cache based on the GIT_COMMIT hash
                writeFile file: "next-lock.cache", text: "$GIT_COMMIT"
        
                cache(caches: [
                    arbitraryFileCache(
                        path: "node_modules",
                        includes: "**/*",
                        cacheValidityDecidingFile: "package-lock.json"
                    )
                ]) {
                    sh "npm install"
                }
            }
        }

        stage('Build') {
            steps {
                // Writes lock-file to cache based on the GIT_COMMIT hash
                writeFile file: 'next-lock.cache', text: "$GIT_COMMIT"

                cache(caches: [
                arbitraryFileCache(
                    path: '.next/cache',
                    includes: '**/*',
                    cacheValidityDecidingFile: 'next-lock.cache'
                )
            ]) {
                    // aka `next build`
                    sh 'npm run build'
            }
            }
        }

        stage('Deploy with PM2') {
            steps {
                sh 'pm2 --name DemoARAIC start npm -- start && pm2 save -f'
            }
        }
    }
}
