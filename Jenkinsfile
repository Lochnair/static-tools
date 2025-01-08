pipeline {
    agent {
        docker {
            args '--group-add abuild'
            image 'lochnair/alpine-sdk:latest'
            reuseNode true
        }
    }
    
    parameters {
        string defaultValue: '5.0.0', description: 'Version', name: 'VERSION', trim: true
    }

    stages {
        stage('Prepare') {
            steps {
                // Set build description
                buildDescription 'screen v${VERSION} - build script commit ${GIT_COMMIT}'

                // Clean before build
                sh 'rm -rf build'
                sh 'mkdir -p build'

                // Install build dependencies
                sh 'doas apk add autoconf automake ncurses ncurses-dev ncurses-static'

                // Download and extract sources
                sh 'wget -O- https://gnuftp.uib.no/screen/screen-${VERSION}.tar.gz | tar --strip-components=1 -xzv -C build'

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
                dir('main/linux-pam') {
                    sh 'env "REPODEST=$WORKSPACE/packages" abuild -r -P'
                }
                sh 'doas apk add $(find ./packages -name "*.apk")'
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
                        strip --strip-unneeded screen
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
