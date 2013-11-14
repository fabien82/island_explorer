#include <iostream>
#include <fstream>
#include <iomanip>
#include <vector>
using namespace std;
int maxc,ntot,nc1;
void concom(void);
ifstream myfile;
ofstream outfile;

int main(){
  outfile.open("lcc_coed.dat",ios::app);  //output file (1 line per execution; to make it a list (for a set of graphs) use a script)
  myfile.open("toto.txt");//input file:each line corresponds to a link, contains indices of both nodes
  concom();
  outfile<<ntot<<"\t"<<maxc<<endl; 
  cout<<"This graph has "<<ntot<<" nodes in "<<nc1<<" connected components, and the largest of them has "<<maxc<<" nodes."<<endl;
}

void concom(){
  int nlink,newca,newcb,newa,newb,testl,nc;
  int niva,nivb;
  double e;
  vector<int> iva,ivb,cardc,ivc,ivd;
  vector<vector<int> > vcc;//vcc stores, for each component(->first argument), the nodes in it(->second argument)
  myfile>>nlink>>e;
  iva.assign(nlink,0);
  ivb.assign(nlink,0);
  cardc.assign(nlink,0);//cardc stores, for each component, the number of nodes in it
  
  nc=1;
  if(nlink>0){
    for(int il=0;il<nlink;il++){//read links one by one
    testl=0;
    ivc.assign(nlink,0);
    myfile>>iva[il]>>ivb[il];
    if(il==0){cardc[0]=2;//first link read->first component, with 2 nodes
      ivd.push_back(iva[il]);ivd.push_back(ivb[il]);
      vcc.push_back(ivd);ivd.clear();}
    else{      
      newca=0;newcb=0;
      for(int ic=0;ic<nc;ic++){//checks if nodes of current link also belong to links read before (i.e. to already existing components)
	if(cardc[ic]!=0&&testl%2==0){
	  for(int j=0;j<cardc[ic];j++){
	    if(iva[il]==vcc[ic][j]){testl+=1;newca=ic;}	    
	  }
	}
	if(cardc[ic]!=0&&testl<2){
	  for(int j=0;j<cardc[ic];j++){
	    if(ivb[il]==vcc[ic][j]){testl+=2;newcb=ic;}
	  }
	}
      }
      if(testl==0){//nodes of new link not found in any existing component->form a new component
	ivd.push_back(iva[il]);ivd.push_back(ivb[il]);
	vcc.push_back(ivd);ivd.clear();
	cardc[nc]=2;
	nc+=1;
      }
      if(testl==1){cardc[newca]+=1;vcc[newca].push_back(ivb[il]);}
      if(testl==2){cardc[newcb]+=1;vcc[newcb].push_back(iva[il]);}
      if(testl==3&&newca!=newcb){//nodes of current link found in 2 distinct components->merge those (arbitrarily->that of index newca)
	for(int i=0;i<cardc[newcb];i++){
	  vcc[newca].push_back(vcc[newcb][i]);}
	cardc[newca]+=cardc[newcb];cardc[newcb]=0;//!!component newcb is emptied, but not deleted
      }
    }//il-test     
    ivc.clear();
  }//il-loop

  maxc=1;
  ntot=0;
  nc1=0;
  for(int i=0;i<nc;i++){
    if(cardc[i]>maxc){maxc=cardc[i];cout<<i<<"\t"<<vcc[i][0]<<"\t"<<vcc[i][1]<<endl;}
    ntot+=cardc[i];
    if(cardc[i]!=0){nc1+=1;}
  }
  }
}//concom
