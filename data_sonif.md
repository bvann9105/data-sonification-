```python
import pandas as pd

filename = 'Pulse Dataset - Sheet1'

df = pd.read_csv('Pulse Dataset - Sheet1.csv')

df = df.sort_values(by=['Victim #'], ascending=False)

df.head() #take a look at first 5 rows
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>Age</th>
      <th>Destiny Number</th>
      <th>life path number</th>
      <th>birthday</th>
      <th>Victim #</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>97</th>
      <td>Jerald A. Wright</td>
      <td>31</td>
      <td>1</td>
      <td>NaN</td>
      <td>1985</td>
      <td>98</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Luis Daniel Wilson-Leon</td>
      <td>37</td>
      <td>1</td>
      <td>9.0</td>
      <td>08.30.1978</td>
      <td>97</td>
    </tr>
    <tr>
      <th>95</th>
      <td>Luis S. Vielma</td>
      <td>22</td>
      <td>7</td>
      <td>2.0</td>
      <td>10.15.1993</td>
      <td>96</td>
    </tr>
    <tr>
      <th>94</th>
      <td>Leroy Valentin Fernandez</td>
      <td>25</td>
      <td>4</td>
      <td>8.0</td>
      <td>09.16.1990</td>
      <td>95</td>
    </tr>
    <tr>
      <th>93</th>
      <td>Shane E. Tomlinson</td>
      <td>33</td>
      <td>3</td>
      <td>7.0</td>
      <td>08.25.1981</td>
      <td>94</td>
    </tr>
  </tbody>
</table>
</div>




```python
import matplotlib.pylab as plt
Age = df['Age'].values   #get age values in an array
victim = df['Victim #'].values  #get diameter values in an array
plt.scatter(victim, Age, s=Age)
plt.xlabel('Age')
plt.ylabel('victim')
plt.show()

age_ordered = max(Age) - Age  #measure time from oldest to youngest
plt.scatter(age_ordered, victim, s=victim)
plt.xlabel('age order')
plt.ylabel('victim')
plt.show()

```


    
![png](output_1_0.png)
    



    
![png](output_1_1.png)
    



```python
def map_value(value, min_value, max_value, min_result, max_result):
  #‘’’maps value (or array of values) from one range to another ‘''
 
 result = min_result + (value - min_value)/(max_value - min_value)*(max_result - min_result)
 return result

map_value(7,1,10,100, 160)
```




    140.0




```python
# option1 compressing time: set conversion factor

vic_per_beat = 1  #conversion factor: Victim for each beat of music

t_data = victim/vic_per_beat #compress impact to beats #use age_ordered for largest to small

t_data
```




    array([98., 97., 96., 95., 94., 93., 92., 91., 90., 89., 88., 87., 86.,
           85., 84., 83., 82., 81., 80., 79., 78., 77., 76., 75., 74., 73.,
           72., 71., 70., 69., 68., 67., 66., 65., 64., 63., 62., 61., 60.,
           59., 58., 57., 56., 55., 54., 53., 52., 51., 50., 49., 48., 47.,
           46., 45., 44., 43., 42., 41., 40., 39., 38., 37., 36., 35., 34.,
           33., 32., 31., 30., 29., 28., 27., 26., 25., 24., 23., 22., 21.,
           20., 19., 18., 17., 16., 15., 14., 13., 12., 11., 10.,  9.,  8.,
            7.,  6.,  5.,  4.,  3.,  2.,  1.])




```python
# option 2 choose the duration in advance (measured in beats) and then map
duration_beats = 52.8 #desired duration 

t_data = map_value( Age, 0, max(Age), 0,duration_beats)

vic_per_beat = max(Age)/duration_beats
print('victims per beat:', vic_per_beat)
```

    victims per beat: 0.946969696969697



```python
#calculate duration in seconds
bpm = 60
duration_sec = duration_beats *60/bpm
print('Duration:', duration_sec, 'seconds')


plt.scatter(t_data, victim, s=Age)
plt.xlabel('time [beats]')
plt.ylabel('victims')
plt.show()
```

    Duration: 52.8 seconds



    
![png](output_5_1.png)
    



```python
#normalize and scaling

y_data = map_value( victim, min(victim), max(victim), 0, 1) 

y_scale = 1

y_data = y_data*y_scale

plt.scatter(victim, t_data, s=50*y_data)
plt.xlabel('age')
plt.ylabel('y data [normalized]')
plt.show()
```


    
![png](output_6_0.png)
    



```python
from audiolazy import str2midi, midi2str

```


```python
str2midi('B0')
```




    23




```python
#petatonic scale 
note_names =  ['C1','C2','G2',
             'C3','E3','G3','A3','B3',
             'D4','E4','G4','A4','B4',
             'D5','E5','G5','A5','B5',
             'D6','E6','F#6','G6','A6']
note_midis = [str2midi(n) for n in note_names] 
note_midis

n_notes= len(note_midis)
print('Resolution:', n_notes, 'notes')
```

    Resolution: 23 notes



```python
#mapping data to midi
midi_data = []
for i in range(len(y_data)) :
    note_index = round(map_value(y_data[i], 0, 1, n_notes-1,0))
    midi_data.append(1*note_midis[note_index])
    
plt.scatter(Age,midi_data, s=10*y_data) #try y_data
plt.xlabel('time[beats]')
plt.ylabel('midi note numbers')
plt.show()
```


    
![png](output_10_0.png)
    



```python
vel_min,vel_max = 40,127   #minimum and maximum note velocity
vel_data = []
for i in range(len(y_data)):
   note_velocity = round(map_value(y_data[i],0,1,vel_min, vel_max)) 
   vel_data.append(note_velocity)
    
plt.scatter(Age*1, midi_data, s=vel_data)
plt.xlabel('time [beats]')
plt.ylabel('midi note numbers')
plt.show()
```


    
![png](output_11_0.png)
    



```python
from midiutil import MIDIFile 
    
#create midi file object, add tempo
my_midi_file = MIDIFile(1) #one track 
my_midi_file.addTempo(track=0, time=0, tempo=bpm) 
#add midi notes
for i in range(len(t_data)):
    my_midi_file.addNote(track=0, channel=0, time=y_data[i], pitch=midi_data[i], volume=vel_data[i], duration=1)
#create and save the midi file itself
with open(filename + '.mid', "wb") as f:
    my_midi_file.writeFile(f)
```


```python


```


```python

```
