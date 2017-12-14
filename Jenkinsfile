#!/usr/bin/env groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            parallel {
                stage('ubuntu17.04') {
                    agent {
                        docker {
                            image 'repo.t0.com/medici/raven-build:v1.0'
                        }
                    }
                    steps {
                        sh '''./autogen.sh
                        ./configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX CXXFLAGS="-fPIC" CPPFLAGS="-fPIC"
                        make -j5

                        mkdir -p build-linux/ubuntu17.04
                        cp src/ravend build-linux/ubuntu17.04/
                        cp src/raven-cli build-linux/ubuntu17.04/
                        cp src/qt/raven-qt build-linux/ubuntu17.04/
                        cd build-linux/ubuntu17.04
                        md5sum ravend raven-cli raven-qt > md5sum
                        sha256sum ravend raven-cli raven-qt > sha256sum
                        tar zcf ubuntu-17.04.${BUILD_NUMBER}.tar.gz ravend raven-cli raven-qt md5sum sha256sum'''
                    }
                }
                stage('ubuntu16.04') {
                    agent {
                        docker {
                            image 'repo.t0.com/medici/raven-build:v0.4'
                        }
                    }
                    steps {
                        sh '''
                        ./autogen.sh
                        ./configure --enable-cxx --disable-shared --with-pic --prefix=$BDB_PREFIX CXXFLAGS="-fPIC" CPPFLAGS="-fPIC"
                        make -j5
                        mkdir -p build-linux/ubuntu16.04
                        cp src/ravend build-linux/ubuntu16.04/
                        cp src/raven-cli build-linux/ubuntu16.04/
                        cp src/qt/raven-qt build-linux/ubuntu16.04/
                        cd build-linux/ubuntu16.04
                        md5sum ravend raven-cli raven-qt > md5sum
                        sha256sum ravend raven-cli raven-qt > sha256sum
                        tar zcf ubuntu-16.04.${BUILD_NUMBER}.tar.gz ravend raven-cli raven-qt md5sum sha256sum'''
                    }
                }
                stage('windows_x64') {
                    agent {
                        docker {
                            image 'repo.t0.com/medici/raven-ubuntu-17.04:v0.2'
                            args  '-v /Users/Shared/Jenkins/Home/raven_depends:/root/depends'
                        }
                    }
                    steps {
                        sh '''
                        echo $HOME
                        PATH=$(echo "$PATH" | sed -e "s/:\\/mnt.*//g")
                        if [ ! -e /root/depends/x86_64-w64-mingw32 ]; then
                            cd depends
                            make -j5 HOST=x86_64-w64-mingw32
                            cd ..
                            ./autogen.sh
                            CONFIG_SITE=$PWD/depends/x86_64-w64-mingw32/share/config.site ./configure --prefix=/
                        else
                            ./autogen.sh
                            CONFIG_SITE=/root/depends/x86_64-w64-mingw32/share/config.site ./configure --prefix=/
                        fi

                        make -j5

                        mkdir /usr/src/raven/build-windows
                        make install DESTDIR=/usr/src/raven/build-windows
                        '''
                    }
                }
                stage('OS_X') {
                    agent any
                    steps {
                        sh '''
                        ./autogen.sh
                        ./configure
                        make -j5'''
                    }
                }
            }
        }
    }
}

