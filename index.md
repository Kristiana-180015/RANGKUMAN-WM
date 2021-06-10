# RANGKUMAN WEB MINING - KD

../README.md

# **Crawling Data :**

***Buka CMD*** 

1. Install scrap dengan cara ketik “pip install scrapy”

2. Scrapy start project scrapy *(nama folder  project yang akan dibuat)*

3. Buka Visual Studio Code untuk membuka folder yang dibuat tadi, lalu jalankan di terminal dengan mengetikkan scrapy genspider jokes (nama file dalam bentuk python yang akan dibuat) lalu copy kan link web yang akan di crawl 

4. lalu ketikkan code dibawah :

   `import scrapy`

   `class JokesSpider(scrapy.Spider):`

     `name= 'jokes'`

     `start_urls = [`

   ​    `'http://www.laughfactory.com/jokes/family-jokes'`

     `]`

     `def parse(self, response):`

   ​    `for joke in response.xpath("//div[@class='jokes']"):`

   ​      `yield{`

   ​        `'joke_text': joke.xpath(".//div[@class='joke-text']/p").extract_first()`

   ​      `}`

   ​    `next_page = response.xpath("//li[@class='next']/a/@haref").extract_first()`

   ​    `if next_page is not None:`

   ​      `next_page_link= response.urljoin(next_page)`

   ​      `yield scrapy.Request(url=next_page_link, callback= self.parse)`

5. lalu ketikkan perintah : scrapy crawl jokes-o datahasil1.json

6. selesai





# Preprocessing

`import re`

`import string`

`import csv`

`import pandas as pd`

`from sklearn.feature_extraction.text import CountVectorizer`

`from sklearn.feature_extraction.text import TfidfTransformer`

`from nltk.tokenize.punkt import PunktSentenceTokenizer`

`from nltk.tokenize import word_tokenize`

`from Sastrawi.Stemmer.StemmerFactory import StemmerFactory`

`from Sastrawi.StopWordRemover.StopWordRemoverFactory import StopWordRemoverFactory`

`from newspaper import Article #melakukan import module newspaper`

 

***`\#Scrapping`***

`def getUrl(address):`

  `url = "http://www.laughfactory.com/jokes/family-jokes"#isi url berita`

  `article = Article(url)  #membuat variabel yang digunakan untuk memanggil Class Article dan menambahkan parameter url`

  `article.download() #melakukan download data dari sebuah halaman website`

  `article.parse() #melakukan proses parsing atau pengambilan berita`

  `berita = article.text`

  `praproses(berita)`

`\#Text Processing`

`def praproses(text): #fungsi preprocesing text`

  *`\#----------- Step1 Case Folding merubah atau menghilangkan karakter yang tidak perlu ------------`*

  `text.lower() #merubah semua huruf ke huruf kecil`

  `text = re.sub(r'\d+', '', text) #menghapus berdasarkan regular expression (angka 0 - 9)`

  *`\#----------- Step2 Tokenizing data / memisahkan setiap kata ------------`*

  `doc_tokenizer = PunktSentenceTokenizer()`

  `sentences_list = doc_tokenizer.tokenize(text)`

  `tempText2 = []`

  `for i in sentences_list:`

​    `getTanslate = i.translate(str.maketrans("","",string.punctuation)) #menghapus tanda baca , . dsb`

​    `tempText2.append(getTanslate) #tokenizing kata dari data text dengan menggunakan nltk tokenizing`

  *`\#----------- Step3 Filtering stopword / penghapusan kata yang tidak penting ------------`*

  `tempText3 = []`

  `factory_stopword = StopWordRemoverFactory()`

  `stopword = factory_stopword.create_stop_word_remover()`

  `for i in tempText2:`

​    `removed = stopword.remove(i)`

​    `tempText3.append(removed)`

  *`*\#----------- Step4 Stemming / menghilangkan infleksi atau kata imbuhan ke bentuk kata dasar ------------*`*

  `hasil = []`

  `factory_stemm = StemmerFactory()`

  `stemm = factory_stemm.create_stemmer()`

  `for i in tempText3:`

​    `stemmed = stemm.stem(i)`

​    `hasil.append(stemmed)`

  `print(hasil)`



  `vsmTfidf(hasil) #melakukan return berupa hasil akhir text yang sudah diproses tadi`

`\#VSM dan TfIdf`

`def vsmTfidf(text): #fungsi vsmtfidf`

  `hasil = text`

  `count = CountVectorizer()`

  `bag = count.fit_transform(hasil)`

  `feature = count.get_feature_names()`

  `a = feature`

  `matrik_vsm = bag.toarray()`

  `dfVsm = pd.DataFrame(data=matrik_vsm,index=list(range(1, len(matrik_vsm[:,1])+1, )),columns=a)`

  `dfVsm.to_csv(r'vsm.csv')`

  `print(dfVsm)`

  `tfidf = TfidfTransformer(use_idf=True, norm='l2', smooth_idf=True) #memanggil class tfidfTransformer dengan parameter dan menyimpannya ke variabel`

  `convertarray = tfidf.fit_transform(count.fit_transform(hasil)).toarray() #menghitung data dari dataframe dan menyimpannya ke variabel dalam bentuk array`

  `print(convertarray) #melakukan print hasil perhitungan`

  `print("=========== Exporting to csv ===========")`

  `dfbtf = pd.DataFrame(data=convertarray, columns=a) #membuat sebuah dataframe baru dengan data yang sudah kita hitung tfidf nya`

  `dfbtf.to_csv(r'tfidftweet.csv') #melakukan export dataframe kedalam csv file`

`getUrl(http://www.laughfactory.com/jokes/family-jokes)`





# Modelling

![](C:\Users\HP\Pictures\id.PNG)

![](C:\Users\HP\Pictures\id1.PNG)

![](C:\Users\HP\Pictures\id2.PNG)







# Evaluasi

***Metode yang digunakan:***

1.  ROUGE-n : Metode ukurannya didasarkan pada recall dan didasarkan pada membandingkan dari n-gram. menggunakan perhitungan dengan ROUGE-n = p/q
2. ROUGE-L : Ukuran ini menggunakan Uprangkaian bersama terpanjang diantara serangkaian teks. Artinya semakin panjang LCS antara dua ringkasan kalimat maka semakin mirip
3. ROUGE-W : mempertimbangkan LCS tetapi tidak memberikan preferensi pada kalimat yang memiliki kata-kata yang lebih berurutan.
4. ROUGE-S : mengukur kejadian bersama skip bigram pada ringkasan rujukan dan ringkasan mesin. Urutan dari bigram diutamakan
5. ROUGE-SU : Kelemahan dari ROUGE-S adalah hanya memperhatikan bigrams. JIka kalimat tidak berisi satu bigram yang overlaping maka tidak a kan menghasilkan bobot pada kalimat. Untuk mengatasi masalah ini ROUGE-S ini maka munculllah ROUGE-SU yang merupakan pengembangannya yang juga memperhatikan unigram

***Implementasi Rouge dengan Python***

- Instalasi Library :

  `pip install git+https://github.com/tagucci/pythonrouge.git atau pip install easy-rouge`

  Lalu ketikkan perintah : 

  `from rouge.rouge import rouge_n_sentence_level`

  `summary_sentence = 'the capital of China is Beijing'.split()` 

  `reference_sentence = 'Beijing is the capital of China'.split()` 

  `\# Calculate ROUGE-2.` 

  `recall, precision, rouge = rouge_n_sentence_level(summary_sentence, reference_sentence, 2)` 

  `print('ROUGE-2-R', recall)` 

  `print('ROUGE-2-P', precision)`

   `print('ROUGE-2-F', rouge)` 

  Output :

  `ROUGE-2-R 0.6`

  `ROUGE-2-P 0.6` 

  `ROUGE-2-F 0.6` 

  `ROUGE-2-R 0.6`

- Implementasi kedua :

  `from rouge import rouge_n_sentence_level`

   `from rouge import rouge_l_sentence_level`

   `from rouge import rouge_n_summary_level`

   `from rouge import rouge_l_summary_level` 

  `from rouge import rouge_w_sentence_level` 

  `from rouge import rouge_w_summary_level` 

  `reference_sentence = 'the police killed the gunman'.split() summary_sentence = 'the gunman police killed'.split()`

   `print('Sentence level:')` 

  `score = rouge_n_sentence_level(summary_sentence, reference_sentence, 1)`
   `print('ROUGE-1: %f' % score.f1_measure)` 

  `_, _, rouge_2 = rouge_n_sentence_level(summary_sentence, reference_sentence, 2)`

   `print('ROUGE-2: %f' % rouge_2)` 

  `_, _, rouge_l = rouge_l_sentence_level(summary_sentence, reference_sentence)` 

  `print('ROUGE-L: %f' % rouge_l)`

   `_, _, rouge_w = rouge_w_sentence_level(summary_sentence, reference_sentence)` 

  `print('ROUGE-W: %f' % rouge_w)`

  Output :

  `Sentence level:` 

  `ROUGE-1: 0.888889`

  `ROUGE-2: 0.571429` 

  `ROUGE-L: 0.666667` 

  `ROUGE-W: 0.550527`



- `from rouge import rouge_n_sentence_level` 

  `from rouge import rouge_l_sentence_level` 

  `from rouge import rouge_n_summary_level`

   `from rouge import rouge_l_summary_level`

   `from rouge import rouge_w_sentence_level` 

  `from rouge import rouge_w_summary_level` 

  `reference_sentences = ['The gunman was shot dead by the police before more people got hurt'.split(),` 

  `'This tragedy causes lives of five , the gunman included'.split(),` 

  `'The motivation of the gunman remains unclear'.split(), ]` 

  `summary_sentences = [ 'Police killed the gunman . no more people got hurt'.split(),` 

  `'Five people got killed including the gunman'.split(),` 

  `'It is unclear why the gunman killed people'.split(), ]` 

  `print('Summary level:')` 

  `_, _, rouge_1 = rouge_n_summary_level(summary_sentences, reference_sentences, 1)` 

  `print('ROUGE-1: %f' % rouge_1)`

   `_, _, rouge_2 = rouge_n_summary_level(summary_sentences, reference_sentences, 2)` 

  `print('ROUGE-2: %f' % rouge_2)`

   `_, _, rouge_l = rouge_l_summary_level(summary_sentences, reference_sentences)` 

  `print('ROUGE-L: %f' % rouge_l)` 

  `_, _, rouge_w = rouge_w_summary_level(summary_sentences, reference_sentences)` 

  `print('ROUGE-W: %f' % rouge_w)`

  Output :

  `Summary level:` 

  `ROUGE-1: 0.400000` 

  `ROUGE-2: 0.188679`

   `ROUGE-L: 0.327273` 

  `ROUGE-W: 0.200430`

  

- `recall, precision, rouge_1 = rouge_n_summary_level(summary_sentences, reference_sentences, 1)` 

  `print('ROUGE-2-R', recall)` 

  `print('ROUGE-2-P', precision)` 

  `print('ROUGE-2-F', rouge)`

  

  Output:

  `ROUGE-2-R 0.36666666666666664`

   `ROUGE-2-P 0.44`

   `ROUGE-2-F 0.6`

