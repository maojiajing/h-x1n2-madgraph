# h-chichi-madgraph

## 0. MadGraph

```
./bin/mg5_amC
import model GldGrv_UFO
generate p p > n2 n2,  (n2 > h01 grv, h01 > b b~) , (n2 > h01 grv, h01 > b b~ )
# or :  generate p p > n2 n2,  (n2 > h01 gld, h01 > b b~) , (n2 > h01 gld, h01 > b b~ )
output ../madgraph_output/MSSM-grv-1/neu2neu2-grv-hbb-hbb
launch
# or :  ./bin/madevent pythia8 run_01
```

### 0.1 Make launch list

```
python make_mass_scan.py -i /home/maojiajing/programs/madgraph_output/MSSM-1d-prod/n3n2-n1-hbb-hbb-0827 -l n3n2-n1-hbb-hbb-0827 -o n3n2-n1-hbb-hbb-m-scan.txt -n 1000 -b 125 -e 1000 -s 25

# output
n3n2-n1-hbb-hbb-m-scan.txt

# run the list
./../MG5_aMC_v2_6_5/bin/mg5_aMC n3n2-n1-hbb-hbb-m-scan.txt
```

## 1. Change lifetime and send to tier2

```
python rerun_lhe_pl.py -i /home/maojiajing/programs/madgraph_output/MSSM-1d-prod/n3n2-n1-hbb-hbb-0827 -l n3n2-n1-hbb-hbb-0827 -o lhe-n3n2-n1-hbb-hbb-m-scan.sh -n 1000 -b 125 -e 150 -s 25 -pl 1000

#run output
source lhe-n3n2-n1-hbb-hbb-m-scan.sh
```

## 2. Copy to tier2 storage

```
python copy_lhe_pl.py -i /home/maojiajing/programs/madgraph_output/MSSM-1d-prod/n3n2-n1-hbb-hbb-0827 -l n3n2-n1-hbb-hbb-0827 -o copy-n3n2-n1-hbb-hbb-m-scan.sh -n 10000 -b 125 -e 1000 -s 25 -pl 10000

scp copy-n3n2-n1-hbb-hbb-m-scan.sh jmao@login-1.hep.caltech.edu:/data/jmao/

#login to tier2, unset cmssw, get grid certificate
source copy-n3n2-n1-hbb-hbb-m-scan.sh 
```

## 3. Change lifetime and test in Delphes

```
python rerun_pythia_pl.py -i /home/maojiajing/programs/madgraph_output/MSSM-1d-prod/n3n2-n1-hbb-hbb-0827 -l n3n2-n1-hbb-hbb-0827 -o rerun-n3n2-n1-hbb-hbb-m-scan.sh -n 1000 -b 125 -e 150 -s 25 -pl 1000

#run output
source rerun-n3n2-n1-hbb-hbb-m-scan.sh
```

### 3.1 Change lifetime in lhe file

```
python ../../bin/internal/lhe_parser.py unweighted_events.lhe.gz unweighted_events_pl_1000.lhe 1000 
gzip unweighted_events_pl_1000.lhe
mv unweighted_events_pl_1000.lhe.gz unweighted_events.lhe.gz
```

### 3.2 Run Pythia8
```
./bin/madevent pythia8 run_01 --tag=pl_1000
```

### 3.3 Run Delphes

```
In directory /home/maojiajing/programs/h-chichi-madgraph/madgraph_output//MSSM-grv-1/neu2neu2-grv-hbb-z-0725/Events/run_01
gunzip Events/run_01/pl_1000_pythia8_events.hepmc.gz
~/programs/h-chichi-madgraph$ ../delphes/DelphesHepMC /home/maojiajing/programs/delphes/cards/delphes_card_CMS.tcl  delphes_output/run_01_pl_1000.root madgraph_output//MSSM-grv-1/neu2neu2-grv-hbb-z-0725/Events/run_01/pl_1000_pythia8_events.hepmc
```

## 4. Check lifetime

```
Delphes->Draw("sqrt( Particle.X*Particle.X + Particle.Y*Particle.Y+Particle.Z*Particle.Z)/(sqrt(Particle.Px[Particle.M1]*Particle.Px[Particle.M1] + Particle.Py[Particle.M1]*Particle.Py[Particle.M1]+Particle.Pz[Particle.M1]*Particle.Pz[Particle.M1])/Particle.E[Particle.M1]*1./sqrt(1-(Particle.Px[Particle.M1]*Particle.Px[Particle.M1] + Particle.Py[Particle.M1]*Particle.Py[Particle.M1]+Particle.Pz[Particle.M1]*Particle.Pz[Particle.M1])/(Particle.E[Particle.M1]*Particle.E[Particle.M1])))", "Particle.PID==5 && Particle.Status==23")
```

