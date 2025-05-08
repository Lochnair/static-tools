pipeline {
    agent {
        docker {
            args '--group-add abuild'
            image 'lochnair/alpine-sdk:latest'
            reuseNode true
        }
    }
    
    parameters {
        string defaultValue: '3.4.1', description: 'Version', name: 'VERSION', trim: true
    }

    stages {
        stage('Prepare') {
            steps {
                // Set build description
                buildDescription "rsync v${params.VERSION} - build script commit ${GIT_COMMIT[0..6]}"


                // Install build dependencies
                sh 'doas apk add autoconf automake acl-dev attr-dev lz4-dev perl popt-dev xxhash-dev zlib-dev zstd-dev'

                // Download and extract sources
                sh "wget -O- https://download.samba.org/pub/rsync/src/rsync-${params.VERSION}.tar.gz | tar --strip-components=1 -xzv"
                sh 'rm -fv testsuite/itemize.test'
                sh 'patch -p1 < dont-use-nobody.patch'

                sh  'LDFLAGS="-static" ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-acl-support --enable-xattr-support --disable-xxhash --with-rrsync --without-included-popt --without-included-zlib --disable-md2man --disable-openssl --disable-zstd --disable-lz4'

                sh '''printf '#!/bin/sh\n\necho "#define RSYNC_GITVER RSYNC_VERSION" >git-version.h\n' >mkgitver'''
            }
        }

        stage('Build') {
            steps {
                // Compile with static flags
                sh '''
                    make -j$(nproc)
                    strip --strip-unneeded rsync
                '''
            }
        }
        
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'rsync', fingerprint: true, followSymlinks: false, onlyIfSuccessful: true
            }
        }
    }
}
