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
# prepare data
#lower case
perl scripts/tokenizer/lowercase.perl < /home/tatik/corpus/corpus.ina > /home/tatik/corpus/train_lower.ina
perl scripts/tokenizer/lowercase.perl < /home/tatik/corpus/corpus.jawa > /home/tatik/corpus/train_lower.jawa
perl scripts/tokenizer/lowercase.perl < /home/tatik/corpus/test.ina > home/tatik/corpus/test_lower.ina
perl scripts/tokenizer/lowercase.perl < /home/tatik/corpus/test.jawa > home/tatik/corpus/test_lower.jawa
tuning
#tokenizer
perl scripts/tokenizer/tokenizer.perl < /home/tatik/corpus/train_lower.jawa > /home/tatik/corpus/train_token.jawa

#clean
perl scripts/training/clean-corpus-n.perl  /home/tatik/corpus/corpus ina jawa /home/tatik/corpus/train_clean 1 80

#build KenLM
bin/lmplz -o 3 </home/tatik/corpus/train_clean.ina >/home/tatik/lm/train_arpa.ina
bin/lmplz -o 3 </home/tatik/corpus/train_clean.jawa >/home/tatik/lm/train_arpa.jawa
#build binary lm for faster
bin/build_binary /home/tatik/lm/train_arpa.jawa /home/tatik/lm/train_blm.jawa
bin/build_binary /home/tatik/lm/train_arpa.ina /home/tatik/lm/train_blm.ina

#cek lm
echo "Awit kejaba wajib isih ana sing dijenengi kautaman!" | bin/query train_blm.jawa
# train
scripts/training/train-model.perl -root-dir /home/tatik/output -corpus /home/tatik/corpus/train_clean -f ina -e jawa -alignment grow-diag-final-and -lm 0:3:/home/tatik/lm/train_blm.ina:8 --external-bin-dir /home/tatik/mgiza/mgizapp/manual-compile/ --mgiza >/home/tatik/output/logoutput1.txt

scripts/training/train-model.perl -root-dir /home/tatik/output -corpus /home/tatik/corpus/train_clean -f jawa -e ina -alignment grow-diag-final-and -lm 0:3:/home/tatik/lm/train_blm.jawa:8 --external-bin-dir /home/tatik/mgiza/mgizapp/manual-compile/ --mgiza
# train 2
~/mosesdecoder$ scripts/training/train-model.perl \
 -root-dir /home/tatik/output \
 -corpus /home/tatik/corpus/train_clean \
 -f ina -e jawa \
 -alignment grow-diag-final-and \
 -reordering msd-bidirectional-fe \
 -lm 0:3:/home/tatik/lm/train_blm.ina:8 \
 --external-bin-dir /home/tatik/mgiza/mgizapp/manual-compile/ \
 --mgiza >/home/tatik/output/logoutput1.txt
Using SCRIPTS_ROOTDIR: /home/tatik/mosesdecoder/scripts
Using multi-thread GIZA
using gzip 
(1) preparing corpus @ Wed May  2 18:12:35 WIB 2018
Executing: mkdir -p /home/tatik/output/corpus
(1.0) selecting factors @ Wed May  2 18:12:35 WIB 2018
(1.1) running mkcls  @ Wed May  2 18:12:35 WIB 2018
/home/tatik/mgiza/mgizapp/manual-compile/mkcls -c50 -n2 -p/home/tatik/corpus/train_clean.ina -V/home/tatik/output/corpus/ina.vcb.classes opt
  /home/tatik/output/corpus/ina.vcb.classes already in place, reusing
(1.1) running mkcls  @ Wed May  2 18:12:35 WIB 2018
/home/tatik/mgiza/mgizapp/manual-compile/mkcls -c50 -n2 -p/home/tatik/corpus/train_clean.jawa -V/home/tatik/output/corpus/jawa.vcb.classes opt
  /home/tatik/output/corpus/jawa.vcb.classes already in place, reusing
(1.2) creating vcb file /home/tatik/output/corpus/ina.vcb @ Wed May  2 18:12:35 WIB 2018
(1.2) creating vcb file /home/tatik/output/corpus/jawa.vcb @ Wed May  2 18:12:35 WIB 2018
(1.3) numberizing corpus /home/tatik/output/corpus/ina-jawa-int-train.snt @ Wed May  2 18:12:35 WIB 2018
  /home/tatik/output/corpus/ina-jawa-int-train.snt already in place, reusing
(1.3) numberizing corpus /home/tatik/output/corpus/jawa-ina-int-train.snt @ Wed May  2 18:12:35 WIB 2018
  /home/tatik/output/corpus/jawa-ina-int-train.snt already in place, reusing
(2) running giza @ Wed May  2 18:12:35 WIB 2018
(2.1a) running snt2cooc ina-jawa @ Wed May  2 18:12:35 WIB 2018

Executing: mkdir -p /home/tatik/output/giza.ina-jawa
Executing: /home/tatik/mgiza/mgizapp/manual-compile/snt2cooc /home/tatik/output/giza.ina-jawa/ina-jawa.cooc /home/tatik/output/corpus/jawa.vcb /home/tatik/output/corpus/ina.vcb /home/tatik/output/corpus/ina-jawa-int-train.snt
line 1000
END.
(2.1b) running giza ina-jawa @ Wed May  2 18:12:35 WIB 2018
/home/tatik/mgiza/mgizapp/manual-compile/mgiza  -CoocurrenceFile /home/tatik/output/giza.ina-jawa/ina-jawa.cooc -c /home/tatik/output/corpus/ina-jawa-int-train.snt -m1 5 -m2 0 -m3 3 -m4 3 -model1dumpfrequency 1 -model4smoothfactor 0.4 -ncpus 4 -nodumps 1 -nsmooth 4 -o /home/tatik/output/giza.ina-jawa/ina-jawa -onlyaldumps 1 -p0 0.999 -s /home/tatik/output/corpus/jawa.vcb -t /home/tatik/output/corpus/ina.vcb
(2.1a) running snt2cooc jawa-ina @ Wed May  2 18:12:35 WIB 2018

Executing: mkdir -p /home/tatik/output/giza.jawa-ina
Executing: /home/tatik/mgiza/mgizapp/manual-compile/snt2cooc /home/tatik/output/giza.jawa-ina/jawa-ina.cooc /home/tatik/output/corpus/ina.vcb /home/tatik/output/corpus/jawa.vcb /home/tatik/output/corpus/jawa-ina-int-train.snt
line 1000
END.
(2.1b) running giza jawa-ina @ Wed May  2 18:12:35 WIB 2018
/home/tatik/mgiza/mgizapp/manual-compile/mgiza  -CoocurrenceFile /home/tatik/output/giza.jawa-ina/jawa-ina.cooc -c /home/tatik/output/corpus/jawa-ina-int-train.snt -m1 5 -m2 0 -m3 3 -m4 3 -model1dumpfrequency 1 -model4smoothfactor 0.4 -ncpus 4 -nodumps 1 -nsmooth 4 -o /home/tatik/output/giza.jawa-ina/jawa-ina -onlyaldumps 1 -p0 0.999 -s /home/tatik/output/corpus/ina.vcb -t /home/tatik/output/corpus/jawa.vcb
(3) generate word alignment @ Wed May  2 18:12:35 WIB 2018
Combining forward and inverted alignment from files:
  /home/tatik/output/giza.ina-jawa/ina-jawa.A3.final.{bz2,gz}
  /home/tatik/output/giza.jawa-ina/jawa-ina.A3.final.{bz2,gz}
Executing: mkdir -p /home/tatik/output/model
Executing: /home/tatik/mosesdecoder/scripts/training/giza2bal.pl -d "gzip -cd /home/tatik/output/giza.jawa-ina/jawa-ina.A3.final.gz" -i "gzip -cd /home/tatik/output/giza.ina-jawa/ina-jawa.A3.final.gz" |/home/tatik/mosesdecoder/scripts/../bin/symal -alignment="grow" -diagonal="yes" -final="yes" -both="yes" > /home/tatik/output/model/aligned.grow-diag-final-and
symal: computing grow alignment: diagonal (1) final (1)both-uncovered (1)
skip=<0> counts=<1010>
(4) generate lexical translation table 0-0 @ Wed May  2 18:12:35 WIB 2018
(/home/tatik/corpus/train_clean.ina,/home/tatik/corpus/train_clean.jawa,/home/tatik/output/model/lex)
  reusing: /home/tatik/output/model/lex.f2e and /home/tatik/output/model/lex.e2f
(5) extract phrases @ Wed May  2 18:12:35 WIB 2018
/home/tatik/mosesdecoder/scripts/generic/extract-parallel.perl 2 split "sort    " /home/tatik/mosesdecoder/scripts/../bin/extract /home/tatik/corpus/train_clean.jawa /home/tatik/corpus/train_clean.ina /home/tatik/output/model/aligned.grow-diag-final-and /home/tatik/output/model/extract 7 orientation --model wbe-msd --GZOutput 
Executing: /home/tatik/mosesdecoder/scripts/generic/extract-parallel.perl 2 split "sort    " /home/tatik/mosesdecoder/scripts/../bin/extract /home/tatik/corpus/train_clean.jawa /home/tatik/corpus/train_clean.ina /home/tatik/output/model/aligned.grow-diag-final-and /home/tatik/output/model/extract 7 orientation --model wbe-msd --GZOutput 
using gzip 
isBSDSplit=0 
Executing: mkdir -p /home/tatik/output/model/tmp.11123; ls -l /home/tatik/output/model/tmp.11123 
split -d -l 506 -a 7 /home/tatik/corpus/train_clean.ina /home/tatik/output/model/tmp.11123/source.split -d -l 506 -a 7 /home/tatik/corpus/train_clean.jawa /home/tatik/output/model/tmp.11123/target.split -d -l 506 -a 7 /home/tatik/output/model/aligned.grow-diag-final-and /home/tatik/output/model/tmp.11123/align.PhraseExtract v1.5, written by Philipp Koehn et al.PhraseExtract v1.5, written by Philipp Koehn et al.
phrase extraction from an aligned parallel corpus

phrase extraction from an aligned parallel corpus
WARNING: sentence 887 has alignment point (12, 11) out of bounds (12, 13)
T: Aja meneh dadi Menteri Agama, mbok dikon nglungguhi kursi sing luwih penting pisan,
S: Jangan lagi jadi Menteri Agama, diminta menduduki kursi yang lebih penting saja,
WARNING: sentence 888 has alignment point (8, 5) out of bounds (8, 7)
T: Pak Nala (mungguh bisaa) ora bakal gelem!
S: Pak Nala (kendati pun bisa) tidak akan bersedia!
WARNING: sentence 889 has alignment point (10, 9) out of bounds (10, 11)
T: Ning saupama-saupami Pak Nala jumeneng (!!) Menteri Agama, banjur kira-kira priye?
S: Tapi seumpamanya Pak Nala menjadi (!!) Menteri Agama, akan bagaimana?
WARNING: sentence 891 has alignment point (9, 4) out of bounds (9, 8)
T: Awit nek diterus-terusake konsekwensine saya angel kanggone negara!
S: Sebab kalau diterus-teruskan konsekuensinya akan makin sulit untuk Negara!
WARNING: sentence 892 has alignment point (10, 9) out of bounds (10, 13)
T: Awit arep ora ngakoni utawa arep ngakoni, hak e (sing nentukake) saka ngendi
S: Sebab mau tidak mengakui atau mengakui, haknya (menentukan) dari mana?
WARNING: sentence 895 has alignment point (4, 5) out of bounds (4, 5)
T: Dadi tegese negara kudu mbayar!!!
S: Artinya Negara harus membayar!!!
WARNING: sentence 898 has alignment point (3, 2) out of bounds (3, 3)
T: Iki dadia peling!
S: Ini perlu diingat!
WARNING: sentence 900 has alignment point (6, 4) out of bounds (6, 6)
T: Kementrian Agama ora ming sugih soal!
S: Kementerian agama tidak hanya kaya masalah!
WARNING: sentence 901 has alignment point (4, 5) out of bounds (7, 5)
T: Kementriane dhewe isih dadi soal!!!!
S: Kementerian agama sendiri ternyata masih menjadi masalah!!!
WARNING: sentence 902 has alignment point (5, 7) out of bounds (6, 6)
T: Tembung dhipinisi kuwi dudu tembung baen-baen!
S: kata definisi bukan merupakan perkara kecil!
WARNING: sentence 904 has alignment point (5, 6) out of bounds (6, 6)
T: Apa bangsane lesung, apa golongane alu?
S: Apa sejenis lesung, apa jenisnya alu?
WARNING: sentence 906 has alignment point (5, 5) out of bounds (5, 5)
T: Awit dhipinisine dhipinisi kuwi apa?
S: Sebab definisinya definisi itu apa?
WARNING: sentence 908 has alignment point (9, 10) out of bounds (10, 10)
T: Ning sanajan ora bisa nerangake rak kena melu nganggo, ta?
S: Tapi kendati tidak bisa menjelaskan boleh ikut memakai kan ?
WARNING: sentence 909 has alignment point (13, 14) out of bounds (13, 16)
T: Saiki tembung dhipinisi kerep kanggo, dadi wong bangsane kaya Pak Nala kiye ya kena bae melu-melu!
S: Sekarang kata definisi sering dipakai, jadi orang seperti Pak Nala juga boleh ikut-ikut!
WARNING: sentence 912 has alignment point (7, 7) out of bounds (7, 7)
T: Ora kena wong liya nglarangi utawa ngluputake!
S: Orang lain tidak boleh melarang atau menyalahkan!
WARNING: sentence 914 has alignment point (6, 6) out of bounds (8, 6)
T: apa iku dudu bebaya njiret dhemokrasi?
S: apa itu bukan berarti bahaya yang menjerat demokrasi?
WARNING: sentence 915 has alignment point (11, 7) out of bounds (11, 11)
T: Awit agama sing cengkah karo dhipinisi mau, apa banjur arep dilarangi?
S: Sebab agama yang bertentangan dengan definisi tersebut, apa kemudian akan dilarang?
WARNING: sentence 916 has alignment point (15, 15) out of bounds (17, 15)
T: Nek ngono manut keyakinane dhewe-dhewe, apa ora jeneng digadhe karo sing padha netepake dhipinisi mau?
S: Kalau begitu kemerdekaan berpikir dan beragama menurut keyakinan masing-masing, apa tidak tergadaikan pada yang menetapkan definisi tersebut.
WARNING: sentence 918 has alignment point (9, 10) out of bounds (10, 10)
T: Lha dalah rak saya ndadra olehe Pamarentah banjur ngicak-ngicak dhemokrasi.
S: Lha dalah, kan semakin berkepanjangan bagi Pemerintah untuk menerapkan demokrasi.
WARNING: sentence 920 has alignment point (10, 10) out of bounds (11, 10)
T: mulane wong ora kena gemampang nyampur adhuk negara karo agama.
S: Makanya orang tidak boleh begitu mudah mencampur aduk Negara dan Agama.
WARNING: sentence 921 has alignment point (12, 12) out of bounds (12, 12)
T: Lho kabeh iki ora teges nek Pak Nala emben-emben arep dadi nabi!
S: Lho semua ini tidak berarti kalau Pak Nala besok akan jadi nabi!
WARNING: sentence 923 has alignment point (8, 7) out of bounds (8, 9)
T: Kejaba nek sebabe merga nabi gadhungan mau gawe onar!
S: Kecuali kalau sebabnya nabi gadungan itu bikin onar!
WARNING: sentence 924 has alignment point (16, 14) out of bounds (16, 16)
T: Wakil Presidhen Amerika Sarekat, Bung R.M. Nixon, sajroning mider-mider ing rat, mampir nyang negarane Pak Nala.
S: Wakil Presiden Amerika Serikat, Bung R.M. Nixon, dalam perjalanan keliling dunia singgah di negaranya Pak Nala.
WARNING: sentence 926 has alignment point (10, 9) out of bounds (10, 10)
T: Wah, bung Nixon iki tamu, "lain dari pada yang lain",
S: Wah Bung Nixon ini tamu "lain dari pada yang lain".
WARNING: sentence 929 has alignment point (13, 12) out of bounds (13, 14)
T: Mengkono uga ana Barabudur, njur ngajak tabikan dulur-dulur sing padha nonton ana pinggir dalan.
S: Begitu juga di Borobudur, mengajak bersalaman dengan orang-orang yang menyaksikan di pinggir jalan.
WARNING: sentence 932 has alignment point (8, 10) out of bounds (9, 10)
T: Dene Pak Nala dhewe ora bisa tabikan karo bung Nixon,
S: Pak Nala sendiri tidak bisa bersalaman dengan bung Nixon.
WARNING: sentence 935 has alignment point (4, 3) out of bounds (4, 7)
T: Wong grapyak kok le benggala gedhe banget!!!"
S: Ramah kok amat sangat!!!”
WARNING: sentence 936 has alignment point (7, 6) out of bounds (8, 6)
T: Pak Nala tas krungu kabar ngene
S: Pak Nala baru saja mendengar kabar sebagai berikut:
WARNING: sentence 938 has alignment point (6, 9) out of bounds (8, 9)
T: le gawe rencana limang taun ki wis ping lima.
S: Rencana kerja lima tahun sudah dibuat lima kali.
WARNING: sentence 939 has alignment point (30, 11) out of bounds (15, 18)
T: Bareng rencana limang taun sing ping lima kuwi rampung, pamarentah Rusia banjur ngedum hadhiyah kalung marang buruh-buruh wanita.
S: Setelah rencana kerja yang kelima selesai. pemerintah Rusia membagikan hadiah berupa kalung untuk buruh-buruh perempuan.
WARNING: sentence 942 has alignment point (17, 18) out of bounds (16, 18)
T: O hiya Pak Nala lali ngandhakake yen merjan gedhe sing keri dhewe diukiri gambar palu arit, simbul kominis.
S: Oh ya, Pak Nala lupa menggambarkan bahwa merjan besar terakhir diberi gambar palu arit, simbol komunis.
WARNING: sentence 946 has alignment point (2, 2) out of bounds (2, 2)
T: Sebabe apa?
S: Apa sebabnya?
WARNING: sentence 948 has alignment point (5, 5) out of bounds (5, 5)
T: Pak Nala nggleges karo mbatin:
S: pak Nala tersenyum sambil membatin,
WARNING: sentence 949 has alignment point (8, 8) out of bounds (8, 8)
T: "E, wong ki nek tresna ora kurang reka.
S: "E, orang itu kalau cinta tidak kehabisan akal.
WARNING: sentence 951 has alignment point (5, 6) out of bounds (6, 6)
T: Pak Sinder kehutanan kecamatan Giritontro aktip.
S: Pak Sinder Kehutanan Kecamatan Giritontro aktif.
WARNING: sentence 953 has alignment point (2, 2) out of bounds (2, 2)
T: Aksi apa?
S: Aksi apa?
WARNING: sentence 954 has alignment point (6, 5) out of bounds (6, 7)
T: Aksi mbrantas kethek-kethek sing padha gawe rerusuh.
S: Aksi memberantas kera-kera yang bikin rusuh.
WARNING: sentence 955 has alignment point (7, 7) out of bounds (7, 7)
T: Pak Nala maca kabar iki bungah atine!
S: Pak Nala membaca berita tersebut dengan gembira.
WARNING: sentence 958 has alignment point (1, 2) out of bounds (1, 2)
T: E, ora!
S: Tidak!
WARNING: sentence 959 has alignment point (3, 3) out of bounds (3, 4)
T: Ing sa Indonesia kabeh!
S: Se Indonesia seluruhnya!
WARNING: sentence 961 has alignment point (3, 2) out of bounds (3, 3)
T: Ing sakabehing kantor
S: Di semua kantor-kantor!
WARNING: sentence 962 has alignment point (6, 6) out of bounds (7, 6)
T: saka kantor gubug tekan kantor gedhe!
S: Mulai dari kantor gubug sampai kantor besar!
WARNING: sentence 966 has alignment point (3, 2) out of bounds (3, 4)
T: Padha bisa tata janma!
S: Semua bisa berbicara!
WARNING: sentence 970 has alignment point (3, 3) out of bounds (3, 3)
T: Kapan bakal wiwite???
S: Kapan akan dimulai????
WARNING: sentence 972 has alignment point (4, 4) out of bounds (4, 4)
T: Ngono batine Pak Nala!
S: Begitu batin Pak Nala!
WARNING: sentence 975 has alignment point (7, 8) out of bounds (7, 9)
T: Dadi ya dudu warga ya dudu pemimpin Partai Katolik.
S: Jadi bukan dan bukan pemimpin Partai Katolik.
WARNING: sentence 978 has alignment point (7, 8) out of bounds (7, 9)
T: Sokur ta nek sing padha maca ora gampangan ngandel!
S: Syukur kalau yang membaca tidak mudah percaya!
WARNING: sentence 981 has alignment point (3, 4) out of bounds (3, 4)
T: Lan komentare gampang bae!
S: Komentarnya mudah saja!
WARNING: sentence 982 has alignment point (5, 4) out of bounds (5, 6)
T: Awit, "Merdeka" dhewe wis menehi dalan.
S: Sebab "Merdeka" sudah membuka jalan.
WARNING: sentence 983 has alignment point (5, 5) out of bounds (6, 5)
T: jare kabar mau saka "Pedoman".
S: Katanya berita tersebut datangnya dari "Pedoman".
WARNING: sentence 984 has alignment point (8, 8) out of bounds (8, 8)
T: Pancen nyata "Pedoman' tanggal 5-11-1953 ngemot ukara iku!
S: Memang betul "Pedoman" tanggal 15-11-1953 memuat kalimat tersebut!
WARNING: sentence 987 has alignment point (5, 5) out of bounds (5, 5)
T: Linduran kanggo ngece "Merdeka" dhewe
S: Igauan untuk mengejek "Merdeka" sendiri!
WARNING: sentence 990 has alignment point (5, 5) out of bounds (5, 5)
T: Ning aja ngethoki ukara sagelem-gelem!
S: Tapi jangan memenggal kalimat semaunya!
WARNING: sentence 993 has alignment point (3, 3) out of bounds (3, 3)
T: Kleru mrega ngantuk!
S: Keliru akibat mengantuk!
WARNING: sentence 994 has alignment point (6, 7) out of bounds (7, 7)
T: Nek ngono suk eneh jangan ngantuk Bung!
S: Jika demikian lain kali jangan ngantuk Bung!
WARNING: sentence 996 has alignment point (8, 4) out of bounds (5, 5)
T: Pak Nala ora arep maido!
S: Pak Nala tidak akan membantah!
WARNING: sentence 998 has alignment point (3, 4) out of bounds (5, 4)
T: Vatikan ora turah-turah dhuwit!
S: Vatikan tidak akan berlebihan uang!
WARNING: sentence 1000 has alignment point (9, 9) out of bounds (10, 9)
T: Ora carane Vatikan campur tangan bab urusaning sawijining Negara!!!
S: Bukan caranya Vatikan untuk turut campur tangan urusannya suatu negara!!!
WARNING: sentence 1002 has alignment point (10, 10) out of bounds (12, 10)
T: lan duwe pengarep-arep, yen ora suwe meneh keamanan bakal beres.
S: dan ada harapan bahwa keamanan dalam waktu tak lama lagi akan beres.
WARNING: sentence 1003 has alignment point (13, 2) out of bounds (13, 16)
T: Pak Nala sing tansah kemecer ngiler marang keamanan kanggo negara lan bangsa melu bungah gedhe banget.
S: Pak Nala yang senantiasa tergiur terhadap keamanan untuk negara dan bangsa sangat gembira.
WARNING: sentence 1004 has alignment point (51, 0) out of bounds (19, 21)
T: Malah jarene pamarentah arep gawe Dewan Keamanan Nasioanal barang lan nyedhiyani dhuwit pirang-pirang juta kanggo mbalekake keamanan sing dicolong para pengaco.
S: Malah kabarnya pemerintah akan membuat Dewan Keamanan Nasional dan menyediakan berjuta-juta uang untuk memulihkan keamanan yang diporakporandakan para pengacau.
WARNING: sentence 1007 has alignment point (7, 7) out of bounds (7, 7)
T: nanging wis pirang-pirang taun keamanan durung pulih-pulih.
S: Namun sudah bertahun-tahun keamanan belum kembali pulih.
WARNING: sentence 1008 has alignment point (9, 7) out of bounds (9, 9)
T: Rumangsane Pak Nala sing ngaco ki kok ana-ana wae.
S: Dugaan Pak Nala, para pengaco itu kok ada-ada saja.


merging extract / extract.inv
gunzip -c /home/tatik/output/model/tmp.11123/extract.0000000.gz /home/tatik/output/model/tmp.11123/extract.0000001.gz  | LC_ALL=C sort     -T /home/tatik/output/model/tmp.11123 2>> /dev/stderr | gzip -c > /home/tatik/output/model/extract.sorted.gz 2>> /dev/stderr 
gunzip -c /home/tatik/output/model/tmp.11123/extract.0000000.inv.gz /home/tatik/output/model/tmp.11123/extract.0000001.inv.gz  | LC_ALL=C sort     -T /home/tatik/output/model/tmp.11123 2>> /dev/stderr | gzip -c > /home/tatik/output/model/extract.inv.sorted.gz 2>> /dev/stderr 
gunzip -c /home/tatik/output/model/tmp.11123/extract.0000000.o.gz /home/tatik/output/model/tmp.11123/extract.0000001.o.gz  | LC_ALL=C sort     -T /home/tatik/output/model/tmp.11123 2>> /dev/stderr | gzip -c > /home/tatik/output/model/extract.o.sorted.gz 2>> /dev/stderr 
Finished Wed May  2 18:12:36 2018
(6) score phrases @ Wed May  2 18:12:36 WIB 2018
(6.1)  creating table half /home/tatik/output/model/phrase-table.half.f2e @ Wed May  2 18:12:36 WIB 2018
/home/tatik/mosesdecoder/scripts/generic/score-parallel.perl 2 "sort    " /home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/extract.sorted.gz /home/tatik/output/model/lex.f2e /home/tatik/output/model/phrase-table.half.f2e.gz  0 
Executing: /home/tatik/mosesdecoder/scripts/generic/score-parallel.perl 2 "sort    " /home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/extract.sorted.gz /home/tatik/output/model/lex.f2e /home/tatik/output/model/phrase-table.half.f2e.gz  0 
using gzip 
Started Wed May  2 18:12:36 2018
/home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/tmp.11166/extract.0.gz /home/tatik/output/model/lex.f2e /home/tatik/output/model/tmp.11166/phrase-table.half.0000000.gz  2>> /dev/stderr 
/home/tatik/output/model/tmp.11166/run.0.sh/home/tatik/output/model/tmp.11166/run.1.shScore v2.1 -- scoring methods for extracted rules
Loading lexical translation table from /home/tatik/output/model/lex.f2e

mv /home/tatik/output/model/tmp.11166/phrase-table.half.0000000.gz /home/tatik/output/model/phrase-table.half.f2e.gzrm -rf /home/tatik/output/model/tmp.11166 
Finished Wed May  2 18:12:38 2018
(6.3)  creating table half /home/tatik/output/model/phrase-table.half.e2f @ Wed May  2 18:12:38 WIB 2018
/home/tatik/mosesdecoder/scripts/generic/score-parallel.perl 2 "sort    " /home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/extract.inv.sorted.gz /home/tatik/output/model/lex.e2f /home/tatik/output/model/phrase-table.half.e2f.gz --Inverse 1 
Executing: /home/tatik/mosesdecoder/scripts/generic/score-parallel.perl 2 "sort    " /home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/extract.inv.sorted.gz /home/tatik/output/model/lex.e2f /home/tatik/output/model/phrase-table.half.e2f.gz --Inverse 1 
using gzip 
Started Wed May  2 18:12:38 2018
/home/tatik/mosesdecoder/scripts/../bin/score /home/tatik/output/model/tmp.11184/extract.0.gz /home/tatik/output/model/lex.e2f /home/tatik/output/model/tmp.11184/phrase-table.half.0000000.gz --Inverse  2>> /dev/stderr 
/home/tatik/output/model/tmp.11184/run.1.sh/home/tatik/output/model/tmp.11184/run.0.shScore v2.1 -- scoring methods for extracted rules
using inverse mode
Loading lexical translation table from /home/tatik/output/model/lex.e2f

gunzip -c /home/tatik/output/model/tmp.11184/phrase-table.half.*.gz 2>> /dev/stderr| LC_ALL=C sort     -T /home/tatik/output/model/tmp.11184  | gzip -c > /home/tatik/output/model/phrase-table.half.e2f.gz  2>> /dev/stderr rm -rf /home/tatik/output/model/tmp.11184 
Finished Wed May  2 18:12:39 2018
(6.6) consolidating the two halves @ Wed May  2 18:12:39 WIB 2018
Executing: /home/tatik/mosesdecoder/scripts/../bin/consolidate /home/tatik/output/model/phrase-table.half.f2e.gz /home/tatik/output/model/phrase-table.half.e2f.gz /dev/stdout | gzip -c > /home/tatik/output/model/phrase-table.gz
Consolidate v2.0 written by Philipp Koehn
consolidating direct and indirect rule tables

Executing: rm -f /home/tatik/output/model/phrase-table.half.*
(7) learn reordering model @ Wed May  2 18:12:40 WIB 2018
(7.1) [no factors] learn reordering model @ Wed May  2 18:12:40 WIB 2018
(7.2) building tables @ Wed May  2 18:12:40 WIB 2018
Executing: /home/tatik/mosesdecoder/scripts/../bin/lexical-reordering-score /home/tatik/output/model/extract.o.sorted.gz 0.5 /home/tatik/output/model/reordering-table. --model "wbe msd wbe-msd-bidirectional-fe"
Lexical Reordering Scorer
scores lexical reordering models of several types (hierarchical, phrase-based and word-based-extraction
(8) learn generation model @ Wed May  2 18:12:40 WIB 2018
  no generation model requested, skipping step
(9) create moses.ini @ Wed May  2 18:12:40 WIB 2018


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

# tuning
~/mosesdecoder/scripts/training/mert-moses.pl ~/moses_baseline_system/corpus/dev/news-test2008.true.fr ~/moses_baseline_system/corpus/dev/news-test2008.true.en ~/mosesdecoder/bin/moses ~/moses_baseline_system/translation_model/model/moses.ini --mertdir ~/mosesdecoder/bin/ --working-dir ~/moses_baseline_system/translation_model/mert-work

scripts/training/mert-moses.pl /home/tatik/corpus/tuning-token-clean.jawa /home/tatik/corpus/tuning-token-clean.ina /home/tatik/mosesdecoder/bin/moses /home/tatik/output/model/moses.ini --mertdir /home/tatik/mosesdecoder/bin/ --working-dir /home/tatik/output/translation_model2/mert-work

# error
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
# testing
 ~/mosesdecoder/scripts/training/filter-model-given-input.pl ~/moses_baseline_system/translation_model/filtered-newstest2011 ~/moses_baseline_system/translation_model/mert-work/moses.ini ~/moses_baseline_system/corpus/test/newstest2011.true.fr
 
 # cek translasi
 bin/moses -f /home/tatik/output/model/moses.ini < in > mt-out
 
~/mosesdecoder/bin/moses -f ~/moses_baseline_system/translation_model/filtered-newstest2011/moses.ini < ~/moses_baseline_system/corpus/test/newstest2011.true.fr > ~/moses_baseline_system/translation_model/newstest2011.translated.en
 
# coba ina
Dan dalam menjalani plonco tersebut tidak hanya beberapa minggu
Dugaan Pak Nala, para pengaco itu kok ada-ada saja
bagaimana dengan kepergian hari ini 
Agaknya rakyat sudah jemu dengan propaganda isapan jempol 

kalenggahaning plonco mau ora hanya sawetara minggu 
Rumangsane Pak Nala, para ngaco iku kok ana-ana bae 
priye karo kepergian dina iki 
jare rakyat wis jemu karo propaganda isapan jempol 
# hasil jawa pakai orientation :9
Lan olehe ngenggoni kalenggahaning plonco mau ora hanya sawetara minggu 
Dugaan Pak Nala, para pengaco apa kok ana-ana bae 
priye karo kepergian dina iki 
jare rakyat wis jemu karo propaganda kuwi mesthi 
# hasil jawa pakai orientation :8
Lan olehe ngenggoni kalenggahaning plonco mau ora hanya sawetara minggu 
Dugaan Pak Nala, para pengaco apa kok ana-ana bae 
priye karo kepergian dina iki 
jare rakyat wis jemu karo propaganda kuwi mesthi 
# hasil jawa pakai mert
Lan olehe ngenggoni kalenggahaning plonco mau ora hanya sawetara minggu 
Dugaan Pak Nala, para pengaco apa kok ana-ana bae 
priye kanthi kepergian dina iki 
jare rakyat wis jemu kanthi propaganda kuwi mesthi 

Liyane demokrasi tuwuh kanthi becik, warga negara Amerika Serikat urip makmur jibar-jibur lan keamanan jinaja kanthi becik
Ning aja ngethoki ukara sagelem-gelem
Mulane ya pantes yen dipengeti nganggo pekan raya barang

Liyane demokrasi tuwuh dengan becik, warga negara Amerika Serikat kehidupan makmur jibar-jibur dan keamanan jinaja dengan baik jika 
tapi jangan memenggal kalimat semau sendiri 
Makanya ya pantas bahwa dipengeti dengan pekan Internasional barang 
