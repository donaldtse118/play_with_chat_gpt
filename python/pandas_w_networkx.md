# Idea
Read a rail scheudle. Generate simple statistic and create network diagram


<img width="400" alt="image" src="https://user-images.githubusercontent.com/486548/216771101-eb2772fc-a859-46cc-b79b-f1b2fb3d9c2d.png">



# Command used
1. load uk railway schedule from feather, with columns current_station, next_station, eta information
2. calculate number of train pass thru for each station-pair
3. calculate number of train visit per current station
4. generate network diagram. set node as station, node's size as number of train visited, set edge as station-pair and its weight as number of train pass thru station-pair


# Output
## Step "1. load uk railway schedule from feather, with columns current_station, next_station, eta information"

Insight
- if you specify enough, ChatGPT could "register" those columns in data for further operation 
- leverage `pandas` library to read feather file
```python
import pandas as pd

df = pd.read_feather('uk_railway_schedule.feather', columns=['current_station', 'next_station', 'eta'])
```

## Step "2. calculate number of train pass thru for each station-pair"
- interpret "station-pair" to column `current_station` and `next_station`
- interpret "number of train" to pandas function `size` 
- rename as `count` as well

Insight 
```python
df['station_pair'] = df['current_station'] + '-' + df['next_station']
station_pair_counts = df.groupby('station_pair').size().reset_index(name='counts')
```

## Step "3. calculate number of train visit per current station"

Insight
- similar as step2
```python
station_counts = df.groupby('current_station').size().reset_index(name='counts')
```

## Step "4. generate network diagram with using plotly. set node as station with name, node's size as number of visited, set edge as station-pair and its weight as number of train pass thru it"

- leverage python library `NetworkX` to create network library to create graph
- interpret "station" in instruction as `current_station` column and set as node
- leverage `split` function on `station-pair` to decompose `current_station` and `next_station` :O (goddnness)
- well "understand" on display, scale node size ten times large by ` * 10`
- set weight label on edge

```python 
# Create a NetworkX graph
G = nx.Graph()

# Add nodes to the graph
for i, row in station_counts.iterrows():
    G.add_node(row['current_station'], size=row['counts'])

# Add edges to the graph
for i, row in station_pair_counts.iterrows():
    current_station, next_station = row['station_pair'].split('-')
    G.add_edge(current_station, next_station, weight=row['counts'])

# Draw the graph
pos = nx.spring_layout(G)
nx.draw(G, pos, node_size=[n[1]['size'] * 10 for n in G.nodes(data=True)], with_labels=True)
labels = nx.get_edge_attributes(G, 'weight')
nx.draw_networkx_edge_labels(G, pos, edge_labels=labels)
plt.show()
```


# Run it
[Colab](https://colab.research.google.com/drive/16KaEW3GX-bBt-zhCOrKOFGSqockNJfLv#scrollTo=S_n0fdMPjWEl)
