:title: Custom_images
:description: A brief description about custom_images
:keywords: custom images, dedicated hosts, shippable images


Custom images
==============

Custom images are open source projects and it is available for all the supported languages. These images are not pre-installed with any tools, addons or services. You will have to customize your shippable.yml file to install it based on the project requirements. Build image from Docker Hub are available at https://registry.hub.docker.com/repos/shippableimages/ . 

The syntax to use custom images:

.. code-block:: bash

  build_image: <docker_hub_username>/<image_name>

.. note::
   If you would need pre-installed tools, addons or services, then you can use shippable/minv2 image.


Below section will give you more details on specific images:

.. note::
 If you want to run builds using your own images, then you will have to enable `Dedicated hosts <http://blog.shippable.com/dedicated-hosts->`_  and you should also update **build_image** tag in shippable.yml file with the path of the image. 
   

**Clojure**
--------------- 

The build image available for clojure is `shippableimages/ubuntu1204_clojure <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_clojure>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_clojure/blob/master/Dockerfile>`_)

The lein version available in this image is lein2

You can use this image to run clojure builds. Configure your shippable.yml file as shown below to use this image.

.. code-block:: bash
  
   build_image: shippableimages/ubuntu1204_clojure

         

A sample yml that helps you getting started with clojure image:

.. code-block:: bash

  language: clojure
  
  lein:
    - lein2  

  #specify the build_image 
  build_image: shippableimages/ubuntu1204_clojure

  script:
    - lein test

Refer `sample_ubuntu1204_clojure <https://github.com/shippableSamples/sample_ubuntu1204_clojure>`_  on github for more details.


**GO**
-------- 

The following build images are available for GOLANG :

1. `shippableimages/ubuntu1204_go <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_go>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_go/blob/master/Dockerfile>`_)


2. `shippableimages/ubuntu1404_go <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_go>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_go/blob/master/Dockerfile>`_)


The go versions available in these images are

-  1.1
-  1.2
-  1.3
-  tip
-  release

You can use these images to run go builds. Add **build_image** tag to your shippable.yml file and activate gvm in before_install section to run your build against the correct version of go. 

A sample yml that helps you getting started with go image:

.. code-block:: bash

  language: go
  
  go:
    - 1.2
    - 1.3  
    
  #specify the build_image 
  build_image: shippableimages/ubuntu1204_go

  before_install:
    - source $HOME/.gvm/scripts/gvm;
    - if [[ $SHIPPABLE_GO_VERSION == "tip" ]]; then gvm install tip; gvm use tip; fi
    - if [[ $SHIPPABLE_GO_VERSION == *release* ]]; then gvm install release; gvm use release; fi
    - if [[ $SHIPPABLE_GO_VERSION =~ [0-9].[0-9] ]]; then gvm install go$SHIPPABLE_GO_VERSION; gvm use go$SHIPPABLE_GO_VERSION; fi
    - export GOPATH=$SHIPPABLE_GOPATH

    - go get github.com/t-yuki/gocover-cobertura
    - go get github.com/onsi/gomega
    - go get github.com/onsi/ginkgo
    - go get code.google.com/p/go.tools/cmd/cover

  install:
    - go get -d -v ./... && go build -v ./...

  script:
    - go test -coverprofile=coverage.txt -covermode count ./
    - $GOPATH/bin/gocover-cobertura < coverage.txt > shippable/codecoverage/coverage.xml

  

Refer `sample_ubuntu1204_go <https://github.com/shippableSamples/sample_ubuntu1204_go>`_  on github for more details.


**Java**
---------

The following build images are available for Java:

1. `shippableimages/ubuntu1204_java <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_java>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_java/blob/master/Dockerfile>`_)

2. `shippableimages/ubuntu1404_java <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_java>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_java/blob/master/Dockerfile>`_)


The JDK's available in these images are

-  openjdk6
-  openjdk7
-  oraclejdk7
-  oraclejdk8

You can use these images to run java builds.  Update your shippable.yml file with **build_image** tag and activate jdk in before_script section to run your build against the correct version.  

A sample yml that helps you getting started with java image:

.. code-block:: bash
  
  language: java
  
  jdk:
    - openjdk6
    - openjdk7
    - oraclejdk7
    - oraclejdk8

  # specify the build_image 
  build_image: shippableimages/ubuntu1204_java

  # install maven 
  before_install:
    - apt-get install -y maven

  # Activate jdk
  before_script:
    - if [[ $SHIPPABLE_JDK_VERSION == "openjdk7" ]] ; then export JAVA_HOME="/usr/lib/jvm/java-7-openjdk-amd64"; export PATH="$PATH:/usr/lib/jvm/java-7-openjdk-amd64/bin"; export java_path="/usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java"; fi
    - if [[ $SHIPPABLE_JDK_VERSION == "oraclejdk7" ]] ; then export JAVA_HOME="/usr/lib/jvm/java-7-oracle"; export PATH="$PATH:/usr/lib/jvm/java-7-oracle/bin"; export java_path="/usr/lib/jvm/java-7-oracle/jre/bin/java"; fi
    - if [[ $SHIPPABLE_JDK_VERSION == "openjdk6" ]] ; then export JAVA_HOME="/usr/lib/jvm/java-6-openjdk-amd64"; export PATH="$PATH:/usr/lib/jvm/java-6-openjdk-amd64/bin"; export java_path="/usr/lib/jvm/java-6-openjdk-amd64/jre/bin/java"; fi
    - if [[ $SHIPPABLE_JDK_VERSION == "oraclejdk8" ]] ; then export JAVA_HOME="/usr/lib/jvm/java-8-oracle"; export PATH="$PATH:/usr/lib/jvm/java-8-oracle/bin"; export java_path="/usr/lib/jvm/java-8-oracle/jre/bin/java"; fi
    - update-alternatives --set java $java_path
    - java -version

  script:
    - mvn test

Refer `sample_ubuntu1204_java <https://github.com/shippableSamples/sample_ubuntu1204_java>`_  on github for more details.


**Node.js**
-----------

The following build images are available for node.js:

1. `shippableimages/ubuntu1204_nodejs <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_nodejs>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_nodejs/blob/master/Dockerfile>`_)

2. `shippableimages/ubuntu1404_nodejs <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_nodejs>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_nodejs/blob/master/Dockerfile>`_)


The node.js versions available in these images are

-   0.8
-   0.10
-   0.11

You can use these images to run node.js builds.  Add **build_image** tag to your shippable.yml file and activate nvm in before_install section to run your build against the correct version of node.js.   


A sample yml that helps you getting started with node.js image:

.. code-block:: bash
 
  language: node_js
  
  node_js:
    - 0.10
    - 0.11

  #specify the build_image
  build_image: shippableimages/ubuntu1204_nodejs

  before_install:
  # Activate the required node.js version
    - source ~/.nvm/nvm.sh && nvm install $SHIPPABLE_NODE_VERSION
    - node --version
    - npm install -g grunt-cli

  #install the required dependencies
  install:
    - npm install

  script:
    - grunt

Refer `sample_ubuntu1204_nodejs <https://github.com/shippableSamples/sample_ubuntu1204_nodejs>`_  on github for more details.




**Python**
--------------- 

The following build images are available for python :

1. `shippableimages/ubuntu1204_python <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_python>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_python/blob/master/Dockerfile>`_)

2. `shippableimages/ubuntu1404_python <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_python>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_python/blob/master/Dockerfile>`_)



The python versions available in these images are

-  2.7.3
-  3.3.5
-  3.4.1


You can use these images to run python builds. Add **build_image** tag to your shippable.yml file and activate the appropriate virtual envrionment in before_install section to run your build against the correct version of python. You can use **$SHIPPABLE_PYTHON** environment variable to specify python versions.

A sample yml that helps you getting started with python image:

.. code-block:: bash

  language: python
  
  python:
    - 2.7
    - 3.3
    - 3.4
 #specify the build_image 
  build_image: shippableimages/ubuntu1204_python

  before_install:
  #  set up a virtualenv and activate the python version that you want to use
    - mkdir -p $HOME/bldve/
    - virtualenv -p $SHIPPABLE_PYTHON  $HOME/bldve/
    - source $HOME/bldve/bin/activate

  install:
  #install the required dependencies
    - pip install -r requirements.txt

  script:
    - python test.py


Refer `sample_ubuntu1204_python <https://github.com/shippableSamples/sample_ubuntu1204_python>`_  on github for more details.


**Ruby**
---------

 
The following build images are available for ruby:

1. `shippableimages/ubuntu1204_ruby <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_ruby>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_ruby/blob/master/Dockerfile>`_)

2. `shippableimages/ubuntu1404_ruby <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_ruby>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_ruby/blob/master/Dockerfile>`_)
 

The ruby versions available in these images are

-  1.8.7
-  1.9.2
-  1.9.3
-  2.0.0
-  2.1.1
-  jruby
-  ruby-head

You can use these images to run ruby builds. Update your shippable.yml file with **build_image** tag and activate rvm in before_install or install section to run your build against the correct version of ruby.  

A sample yml that helps you getting started with ruby image:

.. code-block:: bash
 
  language: ruby

  #specify the build_image 
  build_image: shippableimages/ubuntu1204_ruby

  rvm:
    - 2.1.1

  # activate rvm
  before_install:
    - source ~/.rvm/scripts/rvm
    - rvm install $SHIPPABLE_RUBY --verify-downloads 1
    - source ~/.bashrc && ~/.rvm/scripts/rvm && rvm use $SHIPPABLE_RUBY

  #install the dependencies
  install:
    - bundle install --gemfile="Gemfile"
    - ruby -v

  script:
    - bundle exec rake

Refer `sample_ubuntu1204_ruby <https://github.com/shippableSamples/sample_ubuntu1204_ruby>`_ on github for more details.



**PHP**
--------------- 
The build image available for php is `shippableimages/ubuntu1204_php <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_php>`_  (`Dockerfile <https://github.com/shippableImages/ubuntu1204_php/blob/master/Dockerfile>`_)


The php versions available in this image:

-  5.3
-  5.4
-  5.5
-  5.6

You can use this image to run php builds. Add **build_image** tag to your shippable.yml file and activate the required version in before_install section to run your build against the correct version of php. 


A sample yml that helps you getting started with php image:

.. code-block:: bash

  language: php
  
  php:
    - 5.3
    
  #specify the build_image 
  build_image: shippableimages/ubuntu1204_php

  # Activate the required php version
  before_install:
    - export PATH=$HOME/.phpenv/bin:$HOME/.phpenv/extensions:$PATH && eval "$(phpenv init -)"
    - phpenv global $SHIPPABLE_PHP_VERSION
    - php --version

  script:
    - phpunit  tests/calculator_test.php
  

Refer `sample_ubuntu1204_php <https://github.com/shippableSamples/sample_ubuntu1204_php>`_  on github for more details.
	

**Scala**
--------------- 

The build images available for scala:

1. `shippableimages/ubuntu1204_scala <https://registry.hub.docker.com/u/shippableimages/ubuntu1204_scala>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1204_scala/blob/master/Dockerfile>`_)


2. `shippableimages/ubuntu1404_scala <https://registry.hub.docker.com/u/shippableimages/ubuntu1404_scala>`_ (`Dockerfile <https://github.com/shippableImages/ubuntu1404_scala/blob/master/Dockerfile>`_)


The scala version available in the image is 2.11.2

You can use these images to run scala builds. Add **build_image** tag to your shippable.yml file and tell us what your build image is. 


A sample yml that helps you getting started with scala image:

.. code-block:: bash

  language: scala
  
  #specify the build_image 
  build_image: shippableimages/ubuntu1204_scala
 
  before_script:
    - export PATH=$PATH:$SHIPPABLE_REPO_DIR

  script:
    - export SBT_OPTS="-XX:+CMSClassUnloadingEnabled -XX:PermSize=256M -XX:MaxPermSize=512M"
    - sbt clean scoverage:test
	
Refer `sample_ubuntu1204_scala <https://github.com/shippableSamples/sample_ubuntu1204_scala>`_  on github for more details.



 

