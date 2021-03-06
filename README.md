# HyperMinSketch

Besides being a compact and pretty speedy HyperLogLog implementation for cardinality counting, this modified HyperLogLog allows **intersection** and **similarity** estimation of different HyperLogLogs.

## Details
A simple implementation of HyperLogLog (LogLog-Beta to be specific):
* 16 bit registers instead of 6 bit, the new 10 bit are for b-bit signatures
* Similarity function estimates Jaccard indices (a number between 0-1) of 0.01 for set cardinalities on the order of 1e9 with accuracy around 5%
* Intersection applies the Jaccard index on the union of the sets to return the intersecting set cardinality

*The work is based on ["HyperMinHash: Jaccard index sketching in LogLog space - Yun William Yu, Griffin M. Weber"](https://arxiv.org/pdf/1710.08436.pdf)*

## Example Usage
```go
sk1 := hyperminhash.New()
sk2 := hyperminhash.New()

for i := 0; i < 10000; i++ {
    sk1.Add([]byte(strconv.Itoa(i)))
}

sk1.Cardinality() // 10001 (should be 10000)

for i := 3333; i < 23333; i++ {
    sk2.Add([]byte(strconv.Itoa(i)))
}

sk2.Cardinality()     // 19977 (should be 20000)
sk1.Similarity(sk2)   // 0.284589082 (should be 0.2857326533)
sk1.Intersection(sk2) // 6623 (should be 6667)

sk1.Merge(sk2)
sk1.Cardinality() // 23271 (should be 23333)
```

## Results

### Max Cardinality 1e3

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 350 | 352 | 752 | 752 | 831 | 832 | **271** (32.611312%) | **274** (32.932692%) |
| 746 | 748 | 591 | 590 | 834 | 835 | **503** (60.311751%) | **501** (60.000000%) |
| 248 | 248 | 789 | 791 | 897 | 899 | **140** (15.607581%) | **144** (16.017798%) |
| 9 | 9 | 818 | 818 | 824 | 825 | **3** (0.364078%) | **3** (0.363636%) |
| 408 | 411 | 412 | 408 | 771 | 771 | **49** (6.355383%) | **47** (6.095979%) |


### Max Cardinality 1e4

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 2126 | 2138 | 1162 | 1158 | 3063 | 3060 | **225** (7.345739%) | **223** (7.287582%) |
| 7767 | 7706 | 7054 | 7064 | 8889 | 8887 | **5932** (66.734166%) | **5888** (66.254079%) |
| 842 | 844 | 5183 | 5135 | 5880 | 5842 | **145** (2.465986%) | **135** (2.310852%) |
| 6833 | 6791 | 664 | 666 | 7410 | 7345 | **87** (1.174089%) | **89** (1.211709%) |
| 1814 | 1820 | 6214 | 6169 | 7697 | 7639 | **331** (4.300377%) | **320** (4.189030%) |


### Max Cardinality 1e5

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 29667 | 29540 | 88700 | 88167 | 92444 | 91667 | **25923** (28.041842%) | **25036** (27.311901%) |
| 79242 | 78731 | 30216 | 30137 | 83502 | 82953 | **25956** (31.084285%) | **25995** (31.337022%) |
| 57830 | 57223 | 79550 | 79194 | 82112 | 81595 | **55268** (67.308067%) | **54684** (67.018812%) |
| 64610 | 63501 | 21696 | 21729 | 75895 | 74816 | **10411** (13.717636%) | **10083** (13.477064%) |
| 92204 | 91453 | 96417 | 95556 | 165025 | 163370 | **23596** (14.298440%) | **24130** (14.770154%) |


### Max Cardinality 1e6

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 150443 | 149810 | 974366 | 979514 | 1088517 | 1096991 | **36292** (3.334077%) | **37417** (3.410876%) |
| 156337 | 155347 | 19083 | 19070 | 167353 | 165433 | **8067** (4.820350%) | **8017** (4.846071%) |
| 800969 | 802044 | 51053 | 51244 | 851388 | 853396 | **634** (0.074467%) | **511** (0.059878%) |
| 176155 | 174707 | 520111 | 516822 | 570092 | 569289 | **126174** (22.132217%) | **123766** (21.740452%) |
| 485954 | 481362 | 967341 | 972651 | 1081990 | 1091296 | **371305** (34.316861%) | **376007** (34.455088%) |


### Max Cardinality 1e7

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 7132942 | 7150720 | 122116 | 121539 | 7243153 | 7261709 | **11905** (0.164362%) | **12550** (0.172824%) |
| 8646240 | 8649049 | 1277784 | 1295017 | 9821480 | 9854242 | **102544** (1.044079%) | **99163** (1.006298%) |
| 4192390 | 4164637 | 2788913 | 2779975 | 4526476 | 4499897 | **2454827** (54.232630%) | **2454356** (54.542493%) |
| 9803344 | 9826412 | 1705700 | 1715798 | 10255010 | 10262719 | **1254034** (12.228501%) | **1273821** (12.412120%) |
| 1308849 | 1322604 | 9940327 | 9971519 | 11179030 | 11201850 | **70146** (0.627478%) | **80717** (0.720568%) |


### Max Cardinality 1e8

| Set1 | HLL1 | Set2 | HLL2 | S1 ∪ S2 | HLL1 ∪ HLL2 | S1 ∩ S2 | HLL1 ∩ HLL2 |
|---|---|---|---|---|---|---|---|
| 13237748 | 13298469 | 57073758 | 57124720 | 59474437 | 59394847 | **10837069** (18.221390%) | **11143669** (18.762013%) |
| 90757994 | 88576114 | 5717797 | 5701796 | 95061178 | 93016636 | **1414613** (1.488108%) | **1350058** (1.451416%) |
| 60150663 | 60033013 | 79238333 | 77672994 | 110438475 | 108311818 | **28950521** (26.214162%) | **27666946** (25.543792%) |
| 30187492 | 30718889 | 37756209 | 37153655 | 67443566 | 66938074 | **500135** (0.741561%) | **447406** (0.668388%) |
| 53196095 | 53461989 | 48344583 | 47535284 | 93284291 | 91321031 | **8256387** (8.850780%) | **8036467** (8.800237%) |