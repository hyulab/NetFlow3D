# Net3D
Net3D 是用来decode somatic mutations干嘛的。 具体请参见paper link。我们还有一个web server也可以用来run Net3D on your data

## Installation
Net3D is available on PyPI, which means you can install it with the following command:

	pip install Net3D

Alternatively, you can clone this repository to a local directory:

	git clone https://github.com/zzyingying753/Net3D.git
	cd Net3D

## Prerequisites
The Python Standard Library and the following packages:
- scipy
- numpy
- networkx (2.5 or later)
- pandas
- statsmodels

## Prepare input files
### Required input
- A Mutation Annotation Format (MAF) file (https://docs.gdc.cancer.gov/Data/File_Formats/MAF_Format)

	Necessary columns:
	<ul>
	<li>Hugo_Symbol</li>
	<li>Variant_Classification</li>
	<li>ENSP</li>
	<li>Protein_position</li>
	<li>Codons</li>
	<li>Tumor_Sample_Barcode</li>
	</ul>
	
	Optional columns:
	<ul>
	<li>Transcript_ID</li>
	<li>Gene</li>
	</ul>

	Other columns can also be present in the MAF file but they will not be used. 

### Optional input
- A text file containing a complete list of genes/proteins expressed in the cells where the mutations occur. One ID per line. Gene name, Ensembl gene ID, Ensembl transcript ID, Ensembl protein ID, and UniProt ID are accepted. Example:

	>ENSG00000163166<br>
	>ENSG00000110422<br>
	>ENSG00000077312<br>
	>ENSG00000180660<br>
	>ENSG00000186635<br>

- A text file containing a complete list of protein-protein interactions existing in the cells where the mutations occur. One interaction per line. Protein IDs should be separated by tab. Ensembl protein ID and UniProt ID are accepted. Example:

	>Q9H4A3&emsp;Q9HBL0<br>
	>Q15654&emsp;Q15797<br>
	>P63279&emsp;Q13643<br>
	>O43236&emsp;O43236<br>
	>P01112&emsp;P04049<br>

## Usage
Type your command in the following format:

	python Net3D.py -m <input_maf> -R <resolution> -I <job_name> [-X <expressed_genes>] [-n <binary_interactome>] [-o <output_path>] [-L <logfile_path>] [-t <threads>]

### Required arguments
- `-m <input_maf>`: replace `<input_maf>` with the path to your MAF file.
- `-R <resolution>`: replace `<resolution>` with `low` or `high`. This argument specifies the resolution when reporting subnetworks. If set to `high`, smaller subnetworks are favored. If set to `low`, larger subnetworks are favored.
- `-I <job_name>`: replace `<job_name>` with a preferred name of the current job.

### Optional arguments
- -X <expressed_genes>: replace <expressed_genes> with the path to a text file containing a complete list of expressed genes/proteins (see [Optional input](#optional-input) for details). If not specified, all genes will be considered as expressed.
- -n <binary_interactome>: replace <binary_interactome> with the path to a text file containing a complete list of existing protein-protein interactions (see [Optional input](#optional-input) for details). If not specified, Net3D will use the high quality binary interactome of Homo sapiens curated by HINT (http://hint.yulab.org/).
- -o <output_path>: replace <output_path> with a path where the output files will be put. If not specified, the output files will be stored in `./output/`
- -L <logfile_path>: replace <logfile_path> with a path where the log file will be put. If not specified, the log file will be stored in `./log/`
- -t \<threads>: replace \<threads> with a postive integer. This argument specifies the number of threads to use. If not specified, 5 threads will be used.
	
Example :

	python Net3D.py -m example/input/mutations.maf -R low -I test -X example/input/expressed_genes.txt -n example/input/interactome.txt

## Output files
If -o <output_path> is not specified, the output files will be stored in Net3D/output/. Otherwise the output files will be stored in the specified path. 
- {job_name}`_signatures.txt`:
- {job_name}`_drivers.txt`:
- {job_name}`_subnetworks.txt`:
Three output files will be generated for each job, named as ****, **** and ****.


PINTS can call peaks directly from BAM files. To call peaks from BAM files, you need to provide the tool a path to the bam file and what kind of experiment it was from. If it's from a standard protocol, like PROcap, then you can set --exp-type PROcap. Other supported experiments including GROcap/ CoPRO/ csRNAseq/ NETCAGE/ CAGE/ RAMPAGE/ STRIPEseq. For a complete list of ..., please run

	python Net3D.py -h
user specify:
parser = argparse.ArgumentParser(description = description)
	parser.add_argument('-m','--input_maf', required = True, type = str, help = 'Mutation data in MAF format (MAF file format: https://docs.gdc.cancer.gov/Data/File_Formats/MAF_Format/)')
	parser.add_argument('-R','--resolution', required = True, type = str, help = 'high or low. If set to high, smaller subnetworks are favored. If set to low, larger subnetworks are favored.')
	parser.add_argument('-I','--job_name', required = True, type = str, help = 'Please specify a name for your job. The output files will be stored in a folder with this name')
	parser.add_argument('-t','--threads', type = int, default = 5, help = '[OPTIONAL] The number of threads to use. By default 5')
	parser.add_argument('-X','--expressed_genes', type = str, default = None, help = '[OPTIONAL] A text file with the genes expressed in your context. Each row stands for a gene (HUGO/ENSP/UniProt IDs are accepted). By default all genes are considered as expressed')
	parser.add_argument('-n','--binary_interactome', type = str, default = "./metadata/HomoSapiens_interactome_HINThq.txt", help = '[OPTIONAL] A text file with protein-protein interactions. Each row stands for an interaction (IDs should be separated by tab. HUGO/ENSP/UniProt IDs are accepted). Please include all existing interactions in your context. By default we use the human binary interactome from HINT (http://hint.yulab.org/download/HomoSapiens/binary/hq/)')
	parser.add_argument('-o','--output_path', type = str, default = "./output/", help = '[OPTIONAL] Path to the job folder. If not specified, the job folder will be stored in the default output folder')
	parser.add_argument('-L','--logfile_path', type = str, default = "./log/", help = '[OPTIONAL] Path to the log file. If not specified, the log file will be stored in the default log folder')
	
	Mutation file - Standard .maf with custom coding transcript and protein annotations (ENST00000275493 and p.L858R)

There are only a handful of columns necessary from .maf files. They are:

	Hugo_Symbol

	Chromosome
	
	Start_Position
	
	End_Position
	
	Variant_Classification
	
	Reference_Allele
	
	Tumor_Seq_Allele1
	
	Tumor_Seq_Allele2
	
	Tumor_Sample_Barcode
And two non-standard columns:

	a transcript ID column
	
	a protein peptide change column (HGVS p. single letter abbreviations, ie p.T790M)
Current Annotation Support:

	Transcript ID - Ensembl coding transcript ID's (ENST)

	Gene name - HUGO symbol
