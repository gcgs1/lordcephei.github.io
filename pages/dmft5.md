---
layout: page-fullwidth
title: "Fifth tutorial on QSGW+DMFT"
permalink: "/tutorial/qsgw_dmft/dmft5/"
sidebar: "left"
header: no
---

# The density loop

The standard way (valid for both QSGW and LDA) to close the external loop is by updating the density taking fully into account the dynamical contribution of DMFT, unlike what we have done in the [previous tutorial](https://lordcephei.github.io/tutorial/qsgw_dmft/dmft4). 

### Self-consistent update of the electronic density
Once you obtained a converged *Sig.out.brd*{: style="color: green"} from the DMFT loop, you can can use **lmfdmft**{: style="color: blue"} to sum over all Matsubara's frequencies and update the density until it is self-consistent with the impurity self-energy and the QSGW potential. 

```
mkdir update_scdensity                                           # prepare the folder for the sc-loop
cd update_scdensity
cp ../lmfinput/*.ni  .     
cp ../itN_qmcrun/Sig.out.brd  sig.inp                            # N=index of last DMFT iteration (converged)
lmfdmft ni --ldadc=71.85 -job=1 --rs=1,1 -vbxc0=1 --udrs > log   # update the density using DMFT self-energy and stop
``` 

As a result of the last command, you have an updated density file *rst.ni*{: style="color: green"}. You can monitor the difference with respect to the previous density by 
```
grep 'RMS DQ=' log
```

To achieve self-consistency you shall repeat the last **lmfdmft**{: style="color: blue"} calculation until **RSM DE< 2.0e-4**{: style="color: blue"}, or any other tolerance you may wish although it's not easy to get better than 1.0e-4. To do that you may prefer to insert the last command in a loop like 

```
for ((i=1; i<15 ; i++))
do 
  lmfdmft ni --ldadc=71.85 -job=1 --rs=1,1 -vbxc0=1 --udrs >> log
done
```

### Restarting the QSGW loop 
Now a new LDA or QSGW loop can be done keeping the density fixed. This will lead to a new xc-potential consistent with the density.

+ In the case of an LDA calculation you just want to run **lmf**{: style="color: blue"} with a single iteration (use flags **\-\-rs=1,0 \-vnit=1**{: style="color: blue"}).

+ In the case of a QSGW calculation you will run **lmfgwsc**{: style="color: blue"} with the additional flag **\-\-no-scrho**{: style="color: blue"}.

At the end of either cycle (LDA or QSGW) you are back to the same situation of the [first tutorial](https://lordcephei.github.io/tutorial/qsgw_dmft/dmft1).
