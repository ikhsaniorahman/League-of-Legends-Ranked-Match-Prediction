# League-of-Legends-Ranked-Match-Prediction 
Classification High elo (Challenger) Ranked Games Results using Random Forest and Logistic Regression
## Intro
League of Legends is a MOBA (multiplayer online battle arena) where 2 teams (blue and red) face off. There are 3 lanes, a jungle, and 5 roles. The goal is to take down the enemy Nexus to win the game. This dataset include blue team and red team information from Challenger ranked games, also contains key information that can affect the win or loss in the game. Each game is unique. The gameID can help you to fetch more attributes from the Riot API. The heart of the data is the result of the match between the blue and red teams.
The column blueWins is the target value (the value we are trying to predict). A value of 1 means the blue team has won. 0 otherwise.

> **Explanation Mostly In Bahasa Indonesia**

## Dataset Exploration
Dataset contains 26.904 rows with 50 columns. And the data types are int64 and float64. 

## Data Cleaning
No missing/null values. Only 56 duplicated rows and I dropped it.

## EDA
### Average game length
![image](./assets/images/game%20duration.PNG)


Rata-rata waktu permainan adalah 24 menit. Namun jika dilihat dari plot, ada juga sesi permainan yang hanya berlangsung sekitar 15-18 menit. Hal ini lumrah terjadi pada permainan **"Ranked High Elo"** atau permainan pada ranking tingkat tinggi.
Sementara ada juga beberapa permainan yang hanya selesai dalam waktu kurang lebih 4 menit, hal ini terjadi karena dalam sesi permainan tersebut ada player yang disconnect dan tak kunjung kembali sehingga salah satu tim mengajukan *remake* dan otomatis permainan selesai saat itu juga.

### Correlation between features and target
![image](./assets/images/correlation.PNG)

Saya coba menghitung korelasi kolom-kolom yang terhadap kemenangan/kekalahan tim biru dan merah ("blueWins" dan "redWins").

Semakin mendekati angka 1, maka kolom tersebut semakin berkorelasi satu sama lain. **Walaupun, korelasi ini tidak berarti causation (akibat)** namun jika melihat alur game ini dimana yang menang adalah tim yang dapat menghancurkan bangunan musuh maka hal ini masih makes sense. Proporsi keduanya terlihat cukup seimbang. Entah kita ada di tim merah atau biru, keduanya memiliki porsi yang sama. Dan seperti objektif dasar permainan ini "Hancurkan base musuh = menang" terlihat dari 4 kolom teratas tentang menghancurkan object base musuh.

Contoh kolom "blueTowerKills" memiliki nilai 0.71 terhadap kolom "blueWins" selain berarti kedua kolom ini berkorelasi positif satu sama lain, kolom "blueTowerKills" memiliki peran yang cukup tinggi dalam kemenangan tim biru (tentu saja, semakin banyak tower yang dihancurkan tim biru semakin tinggi kemungkinan untuk menang). 

Namun 5 kolom ini dirasa tidak cukup untuk menggambarkan bagaimana sebuah tim dapat menang. Karena tentu saja menghancurkan tower -> pushing lane -> base musuh hancur -> menang step-step tersebut adalah hal yang lumrah dalam game MOBA, namun kemenangan tim tidak serta merta hanya itu saja kita perlu melihat variabel lainnya.

Dari segi permainan, awalnya saya mengira "Warding" (penempatan ward) atau menghancurkan ward musuh akan menjadi salah satu kunci utama untuk memenangkan permainan, dengan vision yang baik pada map kita dapat mengetahui pergerakan lawan dan mencegah ambush musuh. Namun berdasarkan perhitungan disini warding bukan menjadi variabel kemenangan suatu tim. Selain itu mengamankan area Dragon lebih menguntungkan untuk tim dibandingkan dengan area Baron. Padahal dari segi buff, exp dan gold yang didapatkan lebih besar Baron daripada Dragon. Tim yang berhasil mendapatkan tower pertama berkemungkinan besar akan menang daripada jumlah total kill yang didapatkan.

Dari pengalaman saya bermain game ini, ada beberapa variabel yang memiliki impact juga, antara lain:
- First Tower = Mendapatkan gold lebih untuk player dan tim
- First Blood = Mendapatkan gold lebih dan meningkatkan moral tim
- Dragon/Baron Kills = Objective tambahan untuk mendapatkan buff, gold dan exp
- Ward kills/placed = Untuk map vision dan mencegah ambush
- Total Gold = semakin banyak gold, semakin bagus item yang dimiliki
- Team morals

### Getting First Tower = Win!
![image](./assets/images/First%20tower%20vs%20blue%20win.PNG)


Jika tim biru mendapatkan first tower, proporsi kemenangan yang didapat tim biru lebih besar dibandingkan dengan tidak mendapatkan first tower. Hal ini dikarenakan, jika sebuah tim dapat menghancurkan tower lawan duluan maka tim tersebut akan mendapatkan gold dan exp tambahan. Hal ini tentu sangat menguntungkan untuk tim tersebut.

### Total dragons killed vs Target
![image](./assets/images/totaldragon%20vs%20blue%20win.PNG)


Semakin banyak dragon yang didapatkan oleh tim biru, maka peluang kemenangan semakin besar. Mayoritas kemenangan tim biru diraih dengan mendapatkan minimal 2 dragon.

## Data Preprocessing

### Handling outlier
I detect the outlier with calculating the IQR.
Outlier yang terdeteksi akan di drop agar prediksi model lebih optimal. Kolom gameId dan bluedeaths akan didrop karena tidak dibutuhkan.

Namun tidak semua outlier bisa saya drop karena akan mengurangi banyak rows dari dataset. Sehingga saya hanya akan menghapus outlier di kolom blueKills dengan asumsi jumlah kill diatas Q3(75%) adalah very late game atau game dengan durasi diatas 40 menit. Untuk ukuran Pro Player ini adalah durasi permainan yang terlalu lama.

**Not the best approach, but i wanna know how much model can handle this.**

### Feature Selection
![image](./assets/images/feature%20selection.PNG)


From the results of the correlation matrix, I will take features for modeling that have a small correlation with each other (independent features). The columns are:

1. gameDuration
2. blueFirstBlood
3. blueFirstTower
4. blueFirstBaron
5. blueFirstDragon
6. blueFirstInhibitor
7. blueDragonKills
8. blueBaronKills

### Split dataset and data scalling
I split dataset to 70% train and 30% test and scale the dataset with MinMaxScaler()

## Modelling and Evaluation
I try to use Logistic Regression and Random Forest. 

*Most suitable model for the dataset:* **Random Forest**

![image](./assets/images/Logistic%20Regression.png)
![image](./assets/images/ROC%20AUC.PNG)
![image](./assets/images/confusion%20matrix.PNG)


## Sources of the datasets 
1. [League of Legends Dataset](https://www.kaggle.com/gyejr95/league-of-legends-challenger-ranked-games2020)


