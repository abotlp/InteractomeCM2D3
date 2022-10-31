# InteractomeCM2D3

 Interactome CM2D3  (CoMparative Modeling and Data Driven Docking) database



- GENERATE A NETWORK WITH BIANA 
python generate_network_interactomix.py -radius 0 -taxid  9606 -ttype geneid -trans network.geneid.trans -edge network.edges -node network.nodes -format multi-fields
We can use "generate_network_interactomix.py" to generate the whole network of an organism (-radius =0).
We get the BIANA MAIN NETWORK:
Number of edges: 5260924
Number of nodes: 58200

Also generate the network translated to protein sequence so we can get protein protein interactions and their relative fasta sequences. 

python generate_network_interactomix.py -radius 0 -taxid 9606 -ttype proteinsequence d -trans  network.proteinsequence.trans -edge network.edges -node network.nodes -format multi-fields


- FILTER THE NETWORK
Select only the interactions that have been validated by at least methods of this file
python filter_network.py -n network.edges -rmethod restrictions.txt

30	cross-linking study
397	two hybrid array 
1112	two hybrid prey pooling approach
18	two hybrid
401	biochemical
1356	validated two hybrid
1313	proximity labelling technology
398	two hybrid pooling approach
415	enzymatic study
89	protein array
96	pull down


We obtain a network of 1739568

- SELECT INTERACTIONS VALIDATED BY AT LEAST 3 METHODS: 
We use the script: 
python 3filter.py 

We obtain 37419 interactions 

- TRANSLATE THE NETWORK
python ../NetworkAnalysis/translate_network.py -trans network.uniprotentry.trans -in network.3filtered.edges -on network_uniprot_entry.trans -if multi-fields -of multi-fields

We obtain 37241 translated interactions


- GET PPI AND FASTA FILES
  We can use the script "get_ppi_and_fasta_from_network.py" to get a file of PPIs and a FASTA file from a network.
 python get_ppi_and_fasta_from_network.py -transps <translation file from BIANA IDs to ProteinSequence>  -transhead <translation file from BIANA IDs to another type of ID (optional)> -in <edges input file (not translated)> -if <type of format of the input network> -oppi <PPI file> -ofasta <FASTA file>

python get_ppi_and_fasta_from_network.py -transps ../genomewide/network.proteinsequence.trans  -transhead ../genomewide/network.uniprotentry.trans -in ../genomewide/network.3filtered.edges  -if multi-fields -oppi prova.ppi  -ofasta prova.fasta

- COMPARATIVE MODELING on the  cluster with MODPIN 
python scripts/modppi.py -seq example/3filter.trans.nodes.fasta -ppi example/output.txt -o example/TMP -d ./dummy -v --hydrogens -skip  -force -3did -n 1 --renumerate

- FoldX scoring for models obtained with ModPIN
perl ../get_ddg.v1.pl file_name.pdb


- Generate docking models steps:
RUN M4T

nohup sh runm4t.sh > nohup.txt &

RUN VD2OCK


nohup sh run_vdock_nr.sh > nohup.txt & 

- AlphaFold
Download alphafold database for humans: folder of all pdb + cif files.
Get all the failed models by vdock: simply get the pairs for which the folder is empty.
	sh failed_models.sh

Run phenix to clean the predicted apha fold model from the regions with low LLD score 
	. ../phenix-1.20.1-4487/phenix_env.sh 
	sh run_phenix.sh
 
Run vd2ock 
	nohup sh run_vdock_af.sh > nohup.txt & 

Obtain vdock models rot
vdockmodels.sh










