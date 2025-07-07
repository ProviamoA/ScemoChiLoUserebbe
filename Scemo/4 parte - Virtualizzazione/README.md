## Possibile esercizio 

![alt text](<Screenshot 2025-07-07 alle 15.56.59.png>)

```
1) Numero di CPU disponibili complessivamente senza hyper-threading
Ogni host a 2 CPU con 28 core quindi 56 core fisici e ci sono 10 armadi con 12 host ciascuno quindi 120 host totali

CPUtotali = 120 x 56  =6720 CPU



2)Numero di CPU virtuali disponibili con hyper-threading

Grazie all'hyper-threading ogni core puo gestire 2 thread e quindi ogni core fisico eiuvale a 2 CPU

CPUtotali = 120 x 112 = 13440



3)Numero macchine virtuali (VM) nel datacenter

Numero massimo di VM per vCPU:

NumeroCPU = 13440/8 = 1680


Numero massimo di VM per RAM:



RAM = 61440 / 10 = 6144


Numero\_VM\_RAM = \frac{61440}{10} = 6144 \, VMNumero_VM_RAM=1061440​=6144VM


Il numero massimo di VM è limitato dalle vCPU disponibili:


Numero\_VM\_massimo = 1680 \, VMNumeroVMmassimo=1680VM


4) Si ne necessita  e quindi con i requisiti di 10 GB di RAM e un massimo di 1680 VM , il totale:

RAMnecessaria = 1680 x 10 = 16800



5) la tecnica di HW-assisted puo essere utilizzata



6) Non ricordo



7) Il traffico generato da ogni ToR 

TrafficoToR = 168 x 1.6 = 268.8
```

