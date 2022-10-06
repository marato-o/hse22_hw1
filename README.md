# hse22_hw1

## Работа с сервером
1. Создаём символические ссылки на файлы с помощью команд:
``` bash
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```

2. Создаём папки fastc и multiqc:
``` bash
mkdir fastqc, multiqc
```

3. Производим выбор случайных чтений с randomseed = 721:
``` bash
seqtk sample -721 oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -721 oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -721 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs_1.fastq
seqtk sample -721 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs_2.fastq
```    

4. Производим оценку чтений fastQC и multiQC:
``` bash
ls sub* | xargs -P 2 -tI{} fastqc -o fastqc {}
ls matepairs* | xargs -P 2 -tI{} fastqc -o fastqc {}
multiqc -o multiqc/ fastqc/
```
![image](https://user-images.githubusercontent.com/95280619/194337527-e67ec4ca-af03-4b11-878b-d5d704481a89.png)
![image](https://user-images.githubusercontent.com/95280619/194337657-9a817185-7f8e-48ad-8494-67b0428f1b6a.png)

5. Подрезаем файлы чтения по качеству:
``` bash
platanus_trim sub*
platanus_internal_trim matepairs* matepairs*
```

6. Удаляем ненужные файлы:
``` bash
rm *.fastq
```

7. Производим оценку чтения уже подрезанных файлов и сохраняем их в отдельной папке:
``` bash
mkdir cut_trimmed
ls sub*.trimmed | xargs -P 2 -tI{} fastqc -o cut_trimmed/ {}
ls matepairs*.*trimmed | xargs -P 2 -tI{} fastqc -o cut_trimmed/ {}
```

8. Запускаем анализ multiQC для полученых оценок подрезанных файлов из cut_trimmed:
``` bash
mkdir cut_trimmed_multiqc
multiqc -o cut_trimmed_multiqc/ cut_trimmed/
```
![image](https://user-images.githubusercontent.com/95280619/194337785-98c8fc56-70f4-4574-a700-60be4e0b0667.png)
![image](https://user-images.githubusercontent.com/95280619/194337876-1cf31c11-e635-45c1-b3bc-c18eab73f511.png)

9. Собираем контиги из подрезанных чтений:
``` bash
platanus assemble -f sub*.trimmed
```
    
10. Скачиваем файлы с сервера на локальный компьютер:
``` bash
scp -i /Users/.ssh/bioinf/id_rsa -P 5222 mtomarov@92.242.58.92:/home/mtomarov/out_contig.fa /Users/Marat/Desktop/hw
scp -i /Users/.ssh/bioinf/id_rsa -P 5222 mtomarov@92.242.58.92:/home/mtomarov/out_scaffold.fa /Users/Marat/Desktop/hw
scp -i /Users/.ssh/bioinf/id_rsa -P 5222 mtomarov@92.242.58.92:/home/mtomarov/out_gapClosed.fa /Users/Marat/Desktop/hw
scp -i /Users/.ssh/bioinf/id_rsa -P 5222 mtomarov@92.242.58.92:/home/mtomarov/multiqc.zip /Users/Marat/Desktop/hw
scp -i /Users/.ssh/bioinf/id_rsa -P 5222 mtomarov@92.242.58.92:/home/mtomarov/multiqc.zip /Users/Marat/Desktop/hw
```
## Работа контигами и скаффолдами 
