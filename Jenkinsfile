pipeline {
    agent {
        docker { image 'lochnair/alpine-sdk:latest' }
    }
    
    stages {
        stage('Prepare') {
            steps {
                // Clean before build
                sh 'rm -rf build'
                sh 'mkdir -p build'

                // Install build dependencies
                sh 'su-exec root apk add autoconf automake linux-pam-dev ncurses ncurses-dev'

                // Download and extract sources
                sh 'wget -O- https://gnuftp.uib.no/screen/screen-5.0.0.tar.gz | tar --strip-components=1 -xzv -C build'

                // Run autoconf/autogen script if any
                dir('build') {
                    sh './autogen.sh'
                }
            }
        }
        
        stage('Build') {
            steps {
                dir('build') {
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
