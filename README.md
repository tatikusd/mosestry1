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

# prepare data
perl scripts/training/clean-corpus-n.perl  /home/tatik/corpus/corpus ina jawa /home/tatik/corpus/train_clean 1 80

cmake -DCMAKE_INSTALL_PREFIX=/home/tatik/mgiza -DBoost_ROOT=/home/tatik/mosesdecoder/boost_1_64_0 -DBoost_INCLUDE_DIR=/home/tatik/mosesdecoder/boost_1_64_0/include 

bin/lmplz -o 3 </home/tatik/corpus/train_clean.ina >/home/tatik/lm/train_arpa.ina

bin/lmplz -o 3 </home/tatik/corpus/train_clean.jawa >/home/tatik/lm/train_arpa.jawa

bin/build_binary /home/tatik/lm/train_arpa.jawa /home/tatik/lm/train_blm.jawa

bin/build_binary /home/tatik/lm/train_arpa.ina /home/tatik/lm/train_blm.ina

# train
scripts/training/train-model.perl -root-dir /home/tatik/output -corpus /home/tatik/corpus/train_clean -f ina -e jawa -alignment grow-diag-final-and -lm 0:3:/home/tatik/lm/train_blm.ina:8 --external-bin-dir /home/tatik/mgiza/mgizapp/manual-compile/ --mgiza >/home/tatik/output/logoutput1.txt

scripts/training/train-model.perl -root-dir /home/tatik/output -corpus /home/tatik/corpus/train_clean -f jawa -e ina -alignment grow-diag-final-and -lm 0:3:/home/tatik/lm/train_blm.jawa:8 --external-bin-dir /home/tatik/mgiza/mgizapp/manual-compile/ --mgiza


# hasil cek 
echo "Pak Nala mau membela diri" | /home/tatik/mosesdecoder/bin/query /home/tatik/lm/train_arpa.ina
Loading the LM will be faster if you build a binary file.
Reading /home/tatik/lm/train_arpa.ina
----5---10---15---20---25---30---35---40---45---50---55---60---65---70---75---80---85---90---95--100
****************************************************************************************************
Pak=10 2 -1.0773804	Nala=52 3 -0.14390777	mau=31 3 -2.638905	membela=205 3 -1.4335003	diri=120 3 -0.9190209	</s>=2 1 -1.1629568	Total: -7.375671 OOV: 0
Perplexity including OOVs:	16.954246181795874
Perplexity excluding OOVs:	16.954246181795874
OOVs:	0
Tokens:	6
Name:query	VmPeak:19712 kB	VmRSS:4192 kB	RSSMax:4804 kB	user:0.017188	sys:0.003437	CPU:0.020625	real:0.0205518

#error
================
bin/moses -f /home/tatik/output/model/moses.ini
Defined parameters (per moses.ini or switch):
	config: /home/tatik/output/model/moses.ini 
	distortion-limit: 6 
	feature: UnknownWordPenalty WordPenalty PhrasePenalty PhraseDictionaryMemory name=TranslationModel0 num-features=4 path=/home/tatik/output/model/phrase-table.gz input-factor=0 output-factor=0 Distortion KENLM name=LM0 factor=0 path=/home/tatik/lm/train_blm.ina order=3 
	input-factors: 0 
	mapping: 0 T 0 
	weight: UnknownWordPenalty0= 1 WordPenalty0= -1 PhrasePenalty0= 0.2 TranslationModel0= 0.2 0.2 0.2 0.2 Distortion0= 0.3 LM0= 0.5 
line=UnknownWordPenalty
FeatureFunction: UnknownWordPenalty0 start: 0 end: 0
line=WordPenalty
FeatureFunction: WordPenalty0 start: 1 end: 1
line=PhrasePenalty
FeatureFunction: PhrasePenalty0 start: 2 end: 2
line=PhraseDictionaryMemory name=TranslationModel0 num-features=4 path=/home/tatik/output/model/phrase-table.gz input-factor=0 output-factor=0
FeatureFunction: TranslationModel0 start: 3 end: 6
line=Distortion
FeatureFunction: Distortion0 start: 7 end: 7
line=KENLM name=LM0 factor=0 path=/home/tatik/lm/train_blm.ina order=3
FeatureFunction: LM0 start: 8 end: 8
Loading UnknownWordPenalty0
Loading WordPenalty0
Loading PhrasePenalty0
Loading Distortion0
Loading LM0
Loading TranslationModel0
Start loading text phrase table. Moses format : [0.049] seconds
Reading /home/tatik/output/model/phrase-table.gz
----5---10---15---20---25---30---35---40---45---50---55---60---65---70---75---80---85---90---95--100
****************************************************************************************************
Created input-output object : [0.572] seconds
======================
 #cek translasi
 bin/moses -f /home/tatik/output/model/moses.ini < in > mt-out
# coba in
Dan dalam menjalani plonco tersebut tidak hanya beberapa minggu
Dugaan Pak Nala, para pengaco itu kok ada-ada saja
bagaimana dengan kepergian hari ini 
Agaknya rakyat sudah jemu dengan propaganda isapan jempol 

Lan olehe ngenggoni kalenggahaning plonco mau ora hanya sawetara minggu 
Rumangsane Pak Nala, para ngaco iku kok ana-ana bae 
priye karo kepergian dina iki 
jare rakyat wis jemu karo propaganda isapan jempol 

Liyane demokrasi tuwuh kanthi becik, warga negara Amerika Serikat urip makmur jibar-jibur lan keamanan jinaja kanthi becik
Ning aja ngethoki ukara sagelem-gelem
Mulane ya pantes yen dipengeti nganggo pekan raya barang

Liyane demokrasi tuwuh dengan becik, warga negara Amerika Serikat kehidupan makmur jibar-jibur dan keamanan jinaja dengan baik jika 
tapi jangan memenggal kalimat semau sendiri 
Makanya ya pantas bahwa dipengeti dengan pekan Internasional barang 
