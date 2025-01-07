pipeline {
    agent {
        docker { image 'lochnair/alpine-sdk:latest' }
    }
    
    stages {
        stage('Prepare') {
            steps {
                cleanWs()
                sh 'su-exec root apk add autoconf automake linux-pam-dev ncurses ncurses-dev'
                sh 'wget -O- https://gnuftp.uib.no/screen/screen-5.0.0.tar.gz | tar -xzv'
                dir('screen-5.0.0') {
                    sh './autogen.sh'
                }
            }
        }
        
        stage('Build') {
            steps {
                dir('screen-5.0.0') {
                    sh '''
                        CFLAGS="-flto=auto -static" LDFLAGS="-static" \
                        ./configure \
                            --prefix=/usr \
                            --sysconfdir=/etc \
                            --localstatedir=/var \
                            --enable-colors256 \
                            --enable-telnet \
                            --enable-rxvt_osc
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
