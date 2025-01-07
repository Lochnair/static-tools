pipeline {
    agent {
        docker { image 'lochnair/alpine-sdk:latest' }
    }

    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
    }
    
    stages {
        stage('Prepare') {
            steps {
                 // Clean before build
                cleanWs()
                
                // We need to explicitly checkout from SCM here
                checkout scm

                // Install build dependencies
                sh 'su-exec root apk add autoconf automake linux-pam-dev ncurses ncurses-dev'

                // Download and extract sources
                sh 'wget -O- https://gnuftp.uib.no/screen/screen-5.0.0.tar.gz | tar -xzv'

                // Run autoconf/autogen script if any
                dir('screen-5.0.0') {
                    sh './autogen.sh'
                }
            }
        }
        
        stage('Build') {
            steps {
                dir('screen-5.0.0') {
                    // Compile with static flags
                    sh '''
                        CFLAGS="-flto=auto -static" LDFLAGS="-static" \
                        ./configure \
                            --prefix=/usr \
                            --sysconfdir=/etc \
                            --localstatedir=/var \
                            --enable-colors256 \
                            --enable-telnet \
                            --enable-rxvt_osc
                        make -j8
                    '''
                }
            }
        }
        
        stage('Archive') {
            steps {
                sh 'echo archive'
            }
        }
    }
}
