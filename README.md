# A JARM fork with multithreading  
  
Please read the initial [JARM blog post](https://engineering.salesforce.com/easily-identify-malicious-servers-on-the-internet-with-jarm-e095edac525a) for more information.

The original GitHub repo can be found [here](https://github.com/salesforce/jarm)

This fork was created for adding multithreading to the original project and speed up the processing massive lists of domains or IP addresses.

The file "jarm_jupyter.py" can be imported from a Jupyter notebook to calculate JARM hashes from a Pandas dataframe or list containing the domains/IP addresses to be analyzed 

```Python
from jarm_jupyter import *

# Create domain list from existing dataframe
domain_list = df['DomainSld'].tolist()
# Progress bar widget to track progress
f = widgets.FloatProgress(min=0, max=len(domain_list),description='Progress:')
display(f)
q = Queue(maxsize=0)
# Change the variable below to increase the number of threads if desired
num_threads = 8
for i in range(num_threads):
    worker = Thread(target=jarm_worker, args=(q, f))
    worker.setDaemon(True)
    worker.start()
for entry in domain_list:
    q.put(entry)
        
q.join()

# Create new dataframe with the results
header = ['DomainSld', 'ip', 'jarmHash']
jarm_df = pd.DataFrame(result_list, columns=header)
jarm_df.head()
```

