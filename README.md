# mosestry1
#install required Ubuntu packages to build Moses and its dependencies:
sudo apt-get install build-essential git-core pkg-config automake libtool wget zlib1g-dev python-dev libbz2-dev
#For the regression tests, you'll also need
sudo apt-get install libsoap-lite-perl
#Clone Moses from the repository and cd into the directory for building Moses
git clone https://github.com/moses-smt/mosesdecoder.git
cd mosesdecoder
#Run the following to install a recent version of Boost (the default version on your system might be too old), as well as cmph (for CompactPT), irstlm (language model from FBK, required to pass the regression tests), and xmlrpc-c (for moses server). By default, these will be installed in ./opt in your working directory:
make -f contrib/Makefiles/install-dependencies.gmake
#To compile moses, run
./compile.sh --with-mm

#install giza
git clone https://github.com/moses-smt/mgiza.git
cd mgiza
cd mgizaapp/
cmake .
 cmake -DCMAKE_INSTALL_PREFIX=/home/tatik/mgiza -DBoost_INCLUDE_DIR=/home/tatik/mosesdecoder/boost_1_64_0/include/ 
 cmake -DCMAKE_INSTALL_PREFIX=/home/tatik/mgiza -BOOST_ROOT=/home/tatik/mosesdecoder/boost_1_64_0 -BOOST_INCLUDE_DIR=/home/tatik/mosesdecoder/boost_1_64_0/include 
cmake -DCMAKE_INSTALL_PREFIX=/home/tatik/mgiza -DBoost_ROOT=/home/tatik/mosesdecoder/boost_1_64_0 -DBoost_INCLUDE_DIR=/home/tatik/mosesdecoder/boost_1_64_0/include 
