# hse22_hw1

В начале создаём символические ссылки на файлы с помощью команд: 
    ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
    ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

Затем создаём папки fastc и multiqc:
    mkdir fastqc, multiqc

Производим выбор случайных чтений с randomseed = 721:
    seqtk sample -721 oil_R1.fastq 5000000 > sub1.fastq
    seqtk sample -721 oil_R2.fastq 5000000 > sub2.fastq
    seqtk sample -721 oilMP_S4_L001_R1_001.fastq 1500000 > matepairs_1.fastq
    seqtk sample -721 oilMP_S4_L001_R2_001.fastq 1500000 > matepairs_2.fastq

Производим оценку чтений из файлов:
    ls sub* | xargs -P 2 -tI{} fastqc -o fastqc {}
    ls matepairs* | xargs -P 2 -tI{} fastqc -o fastqc {}

Подрезаем файлы чтения по качеству:
    platanus_trim sub*
    platanus_internal_trim matepairs* matepairs*

Работаем с результатами fastqc и производим multiqc:
    multiqc -o multiqc/ fastqc/

Удаляем ненужные файлы:
    rm *.fastq

Производим оценку чтения уже подрезанных файлов и сохраняем их в отдельной папке:
    mkdir cut_trimmed
    ls sub*.trimmed | xargs -P 2 -tI{} fastqc -o cut_trimmed/ {}
    ls matepairs*.*trimmed | xargs -P 2 -tI{} fastqc -o cut_trimmed/ {}

Запускаем анализ multiqc для полученых оценок подрезанных файлов из cut_trimmed:
    mkdir cut_trimmed_multiqc
    multiqc -o cut_trimmed_multiqc/ cut_trimmed/

Собираем контиги из подрезанных чтений:
    platanus assemble -f sub*.trimmed

