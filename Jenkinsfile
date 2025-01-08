pipeline {
    agent {
        docker {
            args '--group-add abuild'
            image 'lochnair/alpine-sdk:latest'
            reuseNode true
        }
    }
    
    stages {
        stage('Prepare') {
            steps {
                // Clean before build
                sh 'rm -rf build'
                sh 'mkdir -p build'

                // Install build dependencies
                sh 'doas apk add autoconf automake ncurses ncurses-dev ncurses-static'

                // Download and extract sources
                sh 'wget -O- https://gnuftp.uib.no/screen/screen-5.0.0.tar.gz | tar --strip-components=1 -xzv -C build'

                // Run autoconf/autogen script if any
                dir('build') {
                    sh './autogen.sh'
                }
            }
        }
        
        // The default libpam package in Alpine doesn't have static libraries,
        // so let's build our own packages with it enabled
        stage('Libpam') {
            steps {
                sh 'abuild-keygen -a -n -i'
                dir('linux-pam') {
                    sh 'abuild -r'
                    sh 'doas apk add ~/packages/main/*.apk'
                }
            }
        }

        stage('Build') {
            steps {
                dir('build') {
                    // Compile with static flags
                    sh '''
                        LDFLAGS="-static" \
                        ./configure \
                            --prefix=/usr \
                            --sysconfdir=/etc \
                            --localstatedir=/var \
                            --enable-telnet
                        make -j$(nproc)
                    '''
                }
            }
        }
        
        stage('Archive') {
            steps {
                dir('build') {
                    archiveArtifacts artifacts: 'screen', fingerprint: true, followSymlinks: false, onlyIfSuccessful: true
                }
            }
        }
    }
}
