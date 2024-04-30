Create some data
```
data = [{'apple': 10}, {'orange': 15}, {'lemon': 5}, {'lime': 20}]
```


Plot charts with matplotlib
```
python3 -m pip install -U matplotlib
```
Get lists of the names and values of the data
```
names = list(data.keys())
values = list(data.values())
```
Build some charts and display
```
fig, axs = plt.subplots(1, 3, figsize=(9, 3), sharey=True)
axs[0].bar(names, values)
axs[1].scatter(names, values)
axs[2].plot(names, values)
fig.suptitle('Categorical Plotting')
plt.show()
```
