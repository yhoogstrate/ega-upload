Guidelines and practical tips to get your data into EGA, the European Genome-Phenome Archive.


# 0. Find their Readme:

https://www.ebi.ac.uk/ega/submission/tools/EGA_webin

# 1. Make/obtain an upload account

This account is shared, choose password wisely
You have to request it and will receive it some time later

They will send you a form (Submission statement). It has to be signed by the PI and returned.

Only then you will receive credentials and an unlocked (box-)account

->
https://ega-archive.org/submission/quickguide

->
submission form:

https://ega-archive.org/submission-form.php

This process generally takes several days or so.


For members of our neuro-oncology group, we have our own account.
Request username and password with Youri or Pim

# 3. Login in their new portal:

https://www.ebi.ac.uk/ena/submit/sra/#ega

Usename: `box-[0-9]{4}`

Password: ... 


# 4. Register Samples
The samples are not the actual files. Actual files will follow later, linked to the sample id.

https://www.ebi.ac.uk/ena/submit/webin/app-checklist/sample/true

 - Press: **[Download spreadsheet to register samples]**
 - (1) Select: [**EGA Checklists**]
 - (2) Select: [**EGA default checlist**]
 - (3) Select: [**Next**] (Leave to default fields)
 - (4) Press: [**Download TSV Template**]

Open the file `Checklist_EGA-default checklist_[0-9]+.tsv`:

```{txt}
Checklist	ERC000026	EGA default checklist
tax_id	scientific_name	sample_alias	sample_title	sample_description	tax_id	scientific_name	sample_alias	sample_title	sample_description	subject_id	gender	phenotype
#units	 	 	 	 	 	 	 	 	 	 	 	 
```

And fill it in as follows:

```{txt}
#checklist_accession	ERC000026														
sample_alias	tax_id	scientific_name	common_name	sample_title	sample_description	subject_id	gender	phenotype	disease_site	sample type	donor_id	dataset_source	treatment	survival_time_y	survival_status
#units															
belob.10	9606	Homo sapiens	human	10		10	female	glioblastoma	brain	diseased tissue	10	belob	BC 90/110	1.8	Dead
belob.11	9606	Homo sapiens	human	11		11	female	glioblastoma	brain	diseased tissue	11	belob	CCNU	10.8	Dead
belob.13	9606	Homo sapiens	human	13		13	female	glioblastoma	brain	diseased tissue	13	belob	Beva	13.1	Dead
belob.15	9606	Homo sapiens	human	15		15	male	glioblastoma	brain	diseased tissue	15	belob	Beva	17.5	Dead
```

Then:
 - x-check columns in e.g. Word
 - Samples -> [**Register Samples**]
 - Upload filled speadsheet to register samples
 - Choose file, Submit completed Speadsheat -- !! all samples/data should now be CORRECT !!


# 5. Register actual data

Registering data takes place in several steps:

## 5.1 Register Study

Studies (Projects) -> Register study

Save study identifier for later (e.g. `EGAS00001001234`)

## 5.2 Register data files (e.g. fastq.gz)

Prepare your files, concatenate fastq.gz's from multiple runs.
Make sure you have sufficient space on the storage device, encrypted files are about equally large as the original files.
So for 4TB of fastq.gz files, you will need also 4TB of storage for the encrypted files.

### 5.3a encrpyt data files - new method: crypt4gh + inbox

https://ega-archive.org/submission/data/file-preparation/crypt4gh/

All to be uploaded files need to be encrypted.
This should be done one file at the time:

Prepare the environment:

```{sh}
virtualenv -p /usr/bin/python3.9 .venv
source .venv/bin/activate
pip install crypt4gh


echo "-----BEGIN CRYPT4GH PUBLIC KEY-----" > ingestion.pubkey
echo "SUtKgXbC5tBCzM69wvGvFl5qY5OR/+20s5ZyNSebRFw=" >> ingestion.pubkey
echo "-----END CRYPT4GH PUBLIC KEY-----" >> ingestion.pubkey
```

Actual encryption command of two example files:

```{sh}
crypt4gh encrypt --recipient_pk ingestion.pubkey < path/f1_R1.fastq.gz > f1_R1.fastq.gz.c4gh
crypt4gh encrypt --recipient_pk ingestion.pubkey < path/f1_R2.fastq.gz > f1_R2.fastq.gz.c4gh
```

### 5.3a encrpyt data files - method: EGA cryptor v2 java appl

!! Seems fully out of service... all urls of compiled binaries are are broken

https://web2.ega-archive.org/submission/tools/egacryptor (recommended)

https://web2.ega-archive.org/tools

https://github.com/EbiEga/ega-cryptor (no compiled binary available)


Encryption was formerly done using EGA cryptor. Application has difficulties with paths and symlinks and all sorts of stuff, which is probably why they switched eventually.

Download jar file from:

https://github.com/EbiEga/ega-cryptor/packages/1180220


Or download by wget:

```{sh}
wget -o ega-cryptor-2.0.2-test.jar https://github-registry-files.githubusercontent.com/203354144/ac563000-6e3e-11ec-9099-c1023f48a894?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20240105%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20240105T101826Z&X-Amz-Expires=300&X-Amz-Signature=bdc3528b9afa3ca9bd8fa512e15e16dfeb0f2d8841bfb5f3f7bd3c533fc8c9af&X-Amz-SignedHeaders=host&actor_id=0&key_id=0&repo_id=203354144&response-content-disposition=filename%3Dega-cryptor-2.0.2-test.jar&response-content-type=application%2Foctet-stream
```

Examples of encryption command of files individually:

```{sh}
java -jar ega-cryptor-2.0.2-test.jar -i van_Hijfte_GBM_Sample-Y_I1_001.fastq.gz
java -jar ega-cryptor-2.0.2-test.jar -i van_Hijfte_GBM_Sample-Y_R1_001.fastq.gz
java -jar ega-cryptor-2.0.2-test.jar -i van_Hijfte_GBM_Sample-Y_R2_001.fastq.gz
```

Example of encrpyting multiple files using a single command:

```{sh}
java -jar ega-cryptor-2.0.2-test.jar -i "203175700013_R01C01_Grn.idat;203175700013_R01C01_Red.idat;203175700013_R02C01_Grn.idat"
```

## 5.4a upload encrypted files - new inbox method

https://ega-archive.org/submission/data/uploading-files/inbox/

## 5.4b upload encrypted files - old curl method:

Legacy stuff, probably broken? Only compatible with the cryptor files.
You need to upload all three files linked to the original file:
`<filename.ext>.md5`, `<filename.ext>.gpg` and `<filename.ext>.gpg.md5`.

Individual file(s):
```{sh}
curl -T my-local-file.txt.gpg ftp://ftp.ega.ebi.ac.uk/subfolder/ --user ega-box-1234:secret
curl -T my-local-file.txt.md5 ftp://ftp.ega.ebi.ac.uk/subfolder/ --user ega-box-1234:secret
curl -T my-local-file.txt.gpg.md5 ftp://ftp.ega.ebi.ac.uk/subfolder/ --user ega-box-1234:secret
```

Batch wise: by default, the cryptor stores all files in `output-files/`. Adjust
accordingly:
```{sh}
ls output-files/* > files.txt
sed -E "s/(.+)$/curl -T \\1 ftp:\/\/ftp.ega.ebi.ac.uk\/subfolder\/ --user 'ega-box-1234:secret' /g" files.txt > upload.sh
source upload.sh
```

# 5.5 Submit files into EGA portal

 - Go to [**Raw Reads (Experiments and Runs)**]
 - Press [**Submit Reads**]
 - Press [**Download spreadsheet template for Read submission**]
 - For paired-end RNA-seq: [**Submit paired reads using two Fastq files**]
 - Pre-select values you know the much you can, avoids making typo's later
 - Press [**Next**]
 - Press [**Download TSV Template**]

The file `fastq2_template_[0-9+].tsv` looks something like this:

```{txt}
FileType	fastq	Read submission file type
sample	study	instrument_model	library_name	library_source	library_selection	library_strategy	library_layout	forward_file_name	forward_file_md5	reverse_file_name	reverse_file_md5
```

 - Open the file in Libre/OpenOffice or Excel and fill it in:
 - sample: `ERS6643979` - sample identifier
 - study: `EGAS00001001234` - study identifier
 - remove columns `forward_file_md5` and `reverse_file_md5` - md5sums should be uploaded with data, and parsed automatically
 - forward_file_name: `` (path @ EGA FTP)
 - reverse_file_name: `` (path @ EGA FTP)

The final file for G-SAM recovery:
```{txt}
FileType	fastq	Read submission file type
sample	study	instrument_model	library_name	library_source	library_selection	library_strategy	library_layout	forward_file_name	reverse_file_name
ERS6643979	EGAS00001005436	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/AAU2-1951_NGS19-J599_BHM33WDSXX_S51_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/AAU2-1951_NGS19-J599_BHM33WDSXX_S51_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6643995	EGAS00001005436	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/AHA1-1967_NGS19-J598_BHM33WDSXX_S50_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/AHA1-1967_NGS19-J598_BHM33WDSXX_S50_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644004	EGAS00001005436	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/AOA1-1968_NGS19-J600_BHM33WDSXX_S52_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/AOA1-1968_NGS19-J600_BHM33WDSXX_S52_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644006	EGAS00001005436	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/AQA1-1971_NGS19-J556_BHM33WDSXX_S8_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/AQA1-1971_NGS19-J556_BHM33WDSXX_S8_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644014	EGAS00001005436	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/AZD1-2668_NGS19-J595_BHM33WDSXX_S47_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/AZD1-2668_NGS19-J595_BHM33WDSXX_S47_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644051	EGAS00001005437	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/BAS1-2117_NGS19-J614_BHM33WDSXX_S66_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/BAS1-2117_NGS19-J614_BHM33WDSXX_S66_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644056	EGAS00001005437	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/BAW2-2120_NGS19-J616_BHM33WDSXX_S68_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/BAW2-2120_NGS19-J616_BHM33WDSXX_S68_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644057	EGAS00001005437	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/BAX1-2590_NGS19-J568_BHM33WDSXX_S20_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/BAX1-2590_NGS19-J568_BHM33WDSXX_S20_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644092	EGAS00001005437	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/CBV2-2742_NGS19-J572_BHM33WDSXX_S24_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/CBV2-2742_NGS19-J572_BHM33WDSXX_S24_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644102	EGAS00001005437	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/CDD2-2616_NGS19-J561_BHM33WDSXX_S13_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/CDD2-2616_NGS19-J561_BHM33WDSXX_S13_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644107	EGAS00001005438	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/DAB1-2321_NGS19-J613_BHM33WDSXX_S65_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/DAB1-2321_NGS19-J613_BHM33WDSXX_S65_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644108	EGAS00001005438	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/DAB2-2322_NGS19-J585_BHM33WDSXX_S37_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/DAB2-2322_NGS19-J585_BHM33WDSXX_S37_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644113	EGAS00001005438	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAC1-2330_NGS19-J606_BHM33WDSXX_S58_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAC1-2330_NGS19-J606_BHM33WDSXX_S58_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644114	EGAS00001005438	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAC2-2329_NGS19-J577_BHM33WDSXX_S29_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAC2-2329_NGS19-J577_BHM33WDSXX_S29_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644116	EGAS00001005438	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAD2-2192_NGS19-J573_BHM33WDSXX_S25_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAD2-2192_NGS19-J573_BHM33WDSXX_S25_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644117	EGAS00001005439	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAE1-2193_NGS19-J582_BHM33WDSXX_S34_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAE1-2193_NGS19-J582_BHM33WDSXX_S34_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644122	EGAS00001005439	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAG2-2198_NGS19-J555_BHM33WDSXX_S7_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAG2-2198_NGS19-J555_BHM33WDSXX_S7_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644123	EGAS00001005439	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAI1-2201_NGS19-J562_BHM33WDSXX_S14_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAI1-2201_NGS19-J562_BHM33WDSXX_S14_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644128	EGAS00001005439	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAK2-2238_NGS19-J588_BHM33WDSXX_S40_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAK2-2238_NGS19-J588_BHM33WDSXX_S40_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644130	EGAS00001005439	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAL2-2240_NGS19-J605_BHM33WDSXX_S57_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAL2-2240_NGS19-J605_BHM33WDSXX_S57_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644134	EGAS00001005440	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EAP1-2247_NGS19-J591_BHM33WDSXX_S43_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EAP1-2247_NGS19-J591_BHM33WDSXX_S43_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644154	EGAS00001005440	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EBD2-2274_NGS19-J579_BHM33WDSXX_S31_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EBD2-2274_NGS19-J579_BHM33WDSXX_S31_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644178	EGAS00001005440	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/EBY2-2206_NGS19-J617_BHM33WDSXX_S69_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/EBY2-2206_NGS19-J617_BHM33WDSXX_S69_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644182	EGAS00001005440	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/ECB2-2312_NGS19-J575_BHM33WDSXX_S27_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/ECB2-2312_NGS19-J575_BHM33WDSXX_S27_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644191	EGAS00001005440	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/ECH2-2413_NGS19-J590_BHM33WDSXX_S42_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/ECH2-2413_NGS19-J590_BHM33WDSXX_S42_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644198	EGAS00001005441	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/FAB1-2134_NGS19-J580_BHM33WDSXX_S32_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/FAB1-2134_NGS19-J580_BHM33WDSXX_S32_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644201	EGAS00001005441	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/FAD2-2139_NGS19-J578_BHM33WDSXX_S30_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/FAD2-2139_NGS19-J578_BHM33WDSXX_S30_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644205	EGAS00001005441	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/FAG2-2145_NGS19-J581_BHM33WDSXX_S33_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/FAG2-2145_NGS19-J581_BHM33WDSXX_S33_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644216	EGAS00001005441	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/FAM2-2157_NGS19-J569_BHM33WDSXX_S21_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/FAM2-2157_NGS19-J569_BHM33WDSXX_S21_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644223	EGAS00001005441	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAA1-2419_NGS19-J558_BHM33WDSXX_S10_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAA1-2419_NGS19-J558_BHM33WDSXX_S10_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644228	EGAS00001005442	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAG2-2430_NGS19-J565_BHM33WDSXX_S17_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAG2-2430_NGS19-J565_BHM33WDSXX_S17_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644231	EGAS00001005442	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAI1-2433_NGS19-J607_BHM33WDSXX_S59_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAI1-2433_NGS19-J607_BHM33WDSXX_S59_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644233	EGAS00001005442	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAJ1-2435_NGS19-J593_BHM33WDSXX_S45_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAJ1-2435_NGS19-J593_BHM33WDSXX_S45_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644234	EGAS00001005442	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAK1-2437_NGS19-J574_BHM33WDSXX_S26_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAK1-2437_NGS19-J574_BHM33WDSXX_S26_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644235	EGAS00001005442	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAK2-2438_NGS19-J571_BHM33WDSXX_S23_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAK2-2438_NGS19-J571_BHM33WDSXX_S23_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644236	EGAS00001005443	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAL1-2439_NGS19-J584_BHM33WDSXX_S36_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAL1-2439_NGS19-J584_BHM33WDSXX_S36_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644237	EGAS00001005443	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAL2-2440_NGS19-J612_BHM33WDSXX_S64_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAL2-2440_NGS19-J612_BHM33WDSXX_S64_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644239	EGAS00001005443	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAM2-2442_NGS19-J596_BHM33WDSXX_S48_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAM2-2442_NGS19-J596_BHM33WDSXX_S48_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644240	EGAS00001005443	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAN1-2571_NGS19-J594_BHM33WDSXX_S46_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAN1-2571_NGS19-J594_BHM33WDSXX_S46_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644242	EGAS00001005443	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAO1-2445_NGS19-J586_BHM33WDSXX_S38_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAO1-2445_NGS19-J586_BHM33WDSXX_S38_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644244	EGAS00001005444	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAP1-2447_NGS19-J564_BHM33WDSXX_S16_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAP1-2447_NGS19-J564_BHM33WDSXX_S16_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644245	EGAS00001005444	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAP2-2448_NGS19-J604_BHM33WDSXX_S56_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAP2-2448_NGS19-J604_BHM33WDSXX_S56_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644246	EGAS00001005444	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAQ1-2449_NGS19-J608_BHM33WDSXX_S60_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAQ1-2449_NGS19-J608_BHM33WDSXX_S60_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644249	EGAS00001005444	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAR2-2452_NGS19-J597_BHM33WDSXX_S49_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAR2-2452_NGS19-J597_BHM33WDSXX_S49_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644251	EGAS00001005444	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAS2-2454_NGS19-J515_AHM2NJDSXX_S242_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAS2-2454_NGS19-J515_AHM2NJDSXX_S242_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644251	EGAS00001005445	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/GAS2-repl_NGS19-J576_BHM33WDSXX_S28_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/GAS2-repl_NGS19-J576_BHM33WDSXX_S28_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644264	EGAS00001005445	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/HAH1-2533_NGS19-J570_BHM33WDSXX_S22_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/HAH1-2533_NGS19-J570_BHM33WDSXX_S22_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644269	EGAS00001005445	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/JAC1-2703_NGS19-J592_BHM33WDSXX_S44_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/JAC1-2703_NGS19-J592_BHM33WDSXX_S44_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644272	EGAS00001005445	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/JAD2-2706_NGS19-J559_BHM33WDSXX_S11_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/JAD2-2706_NGS19-J559_BHM33WDSXX_S11_L002_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644276	EGAS00001005445	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/JAF2-2711_NGS19-J587_BHM33WDSXX_S39_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/JAF2-2711_NGS19-J587_BHM33WDSXX_S39_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644282	EGAS00001005446	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/JAI2-2717_NGS19-J618_BHM33WDSXX_S70_L003_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/JAI2-2717_NGS19-J618_BHM33WDSXX_S70_L003_R2_001.fastp-recovered.fastq.gz.gpg
ERS6644299	EGAS00001005446	Illumina NovaSeq 6000	NGS19	TRANSCRIPTOMIC	cDNA	RNA-Seq	PAIRED	gsam-recovered/KAD2-2698_NGS19-J560_BHM33WDSXX_S12_L002_R1_001.fastp-recovered.fastq.gz.gpg	gsam-recovered/KAD2-2698_NGS19-J560_BHM33WDSXX_S12_L002_R2_001.fastp-recovered.fastq.gz.gpg
```

Go back to the main page to upload the tsv file:

 - Go to [**Raw Reads (Experiments and Runs)**]
 - Press [** + Submit Reads**]
 - Press [**Upload filled spreadsheet template for Read submission**] + Choose file
 - Fill in Center name (e.g. `Erasmus MC`)

When you receive errors as these:
```{txt}
In run, alias: "ena-RUN-TAB-11-01-2024-12:33:44:658-242573". File "gsam-recovered/GAI1-2433_NGS19-J607_BHM33WDSXX_S59_L003_R1_001.fastp-recovered.fastq.gz" does not exist in the upload area.
``` 

It does NOT automatically mean the file is not uploaded, it means that Webin does not have it (?yet) in its cache/database.
The best thing is to leave it a day or so and try it again ... because the cache does not get synchronised directly.
If waiting does not resolve this, the files are somehow indeed stuck - this unfortunately also happens quite often.
In that case, send an e-mail to the help-desk and wait for ~3 working days to have this resolved.


# 6. DAC etc
Add data access policy - either text field or title does not support the '&'-character

