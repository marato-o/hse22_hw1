# hse22_hw1

## Работа на сервере
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
![image](https://user-images.githubusercontent.com/95280619/194399780-cce3f816-4ce1-4f77-a743-9f8021764fac.png)
![image](https://user-images.githubusercontent.com/95280619/194399830-6e26f69f-03f0-49f6-8ac1-76227ad4203a.png)
![image](https://user-images.githubusercontent.com/95280619/194337657-9a817185-7f8e-48ad-8494-67b0428f1b6a.png)
![image](https://user-images.githubusercontent.com/95280619/194399899-c589296a-bafe-4dc3-a0a6-7f1d5d778f4a.png)
![image](https://user-images.githubusercontent.com/95280619/194399985-f61dd38c-98c9-42bd-810e-9b3ba6bf0dab.png)
![image](https://user-images.githubusercontent.com/95280619/194400796-b1075a18-c1cb-4ce8-ba28-8bd3cd815f58.png)



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
![image](https://user-images.githubusercontent.com/95280619/194400410-1d03d144-0340-49fa-8d06-b7815cc7adf5.png)
![image](https://user-images.githubusercontent.com/95280619/194400457-f253a761-d89c-4d5c-9fdf-29019f08e14d.png)
![image](https://user-images.githubusercontent.com/95280619/194337876-1cf31c11-e635-45c1-b3bc-c18eab73f511.png)
![image](https://user-images.githubusercontent.com/95280619/194400513-9cd180bd-a363-4aad-bc94-a5b48f4edda0.png)
![image](https://user-images.githubusercontent.com/95280619/194400568-d3ee7652-e162-4888-a2bc-b4eef8ac26d0.png)
![image](https://user-images.githubusercontent.com/95280619/194400852-d64dd3fd-42d6-4b40-b4a5-caf205dc0a92.png)
![image](https://user-images.githubusercontent.com/95280619/194400893-cf02babb-f30a-49cf-a7b7-57bb61853ec1.png)

9. Собираем контиги из подрезанных чтений:
``` bash
platanus assemble -f sub*.trimmed
```

10. Собираем скаффолды из контигов и подрезанных чтений:
``` bash
time platanus scaffold -c out_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed
```

11. Собираем скаффолды с меньшим кол-вом пропусков:
``` bash
time platanus gap_close -c out_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed
```

12. Скачиваем файлы с сервера на локальный компьютер:
``` bash
scp -i dir/ssh -P X mtomarov@IP:/home/mtomarov/my_file dir
```
## Анализ контигов и скаффолдов 
https://colab.research.google.com/drive/1ELYo6sOceFQ_bp48jRFRBwEasc98PIZX?usp=sharing

### Анализ контигов
![image](https://user-images.githubusercontent.com/95280619/194399290-49e74c64-d5cf-4128-bdee-de771c4bea90.png)
![image](https://user-images.githubusercontent.com/95280619/194399371-4f13263e-6fa2-43e4-a431-c9078362e426.png)

### Анализ скаффолдов
![image](https://user-images.githubusercontent.com/95280619/194399505-4e4c30c7-0611-490f-a828-2f44751344f3.png)
![image](https://user-images.githubusercontent.com/95280619/194399587-546734a4-38ce-4a03-a7ba-dbfebff5e4cc.png)


## Анализ контигов и скаффолдов 
