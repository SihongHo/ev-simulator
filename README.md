# Rebalnacing Expanding EV sharing System with Deep Reinforcement Learning

Our paper can be found [here](https://www.ijcai.org/Proceedings/2020/0186.pdf). An extended version is under revision for ACM TIST.


## Prerequisites
```
- Python 3.7
- utm 0.5.0
- pandas 0.25.0
- kdtree 0.16
- numpy 1.16.4
- prettytable 0.7.2
```

### Using Pip
```
pip install -r requirements.txt
```
### Using Conda
```
conda env create -f environment.yaml
conda activate vehicle3.7
```


## Directory

```
ev-reb-simulator/
├── data
│   ├── hex_pos_info.csv                # Hexagon partition of the space
│   ├── orders_one_week.pkl             # Sample user orders (demand) within one week
│   ├── station_seq_info.csv            # EV station properties
│   └── daily_online_offline.pkl        # Station dynamics within one week
├── environment.yaml                    # Configuration file for conda
├── logs
│   └── log.txt                         # Log output by the simulator
├── README.md
├── requirements.txt                    # Configuration file for pip
├── run.py                              # Main entry of the simulator
└── src
    ├── env.py                          # Main environment of the simulator
    └── utils
        ├── car.py                      # EV 
        ├── order.py                    # Order 
        └── station.py                  # Station 
```

## Run
```
python run.py
```


## Simulator Details

### Environment
The simulator operates on a 2D space with hexagonal partition with discrete time. There are three key elements: 
1. Shared EVs with certain range and charging time
2. EV Stations with different numbers of charging docks (parking spaces)
3. User orders with describes their demand at different timestamps


### Data
We also provide a set of sample data for the simulator to run. In practice, such data can be generated by learning from the real operation logs of the EV sharing system. More concretely, we provide the following data:
1. Geometric details of the hexagon partition (`hex_pos_info.csv`)
2. Properties of EV stations (`station_seq_info.csv`)
3. User demand in one week (`orders_one_week.pkl`)
4. Station network dynamics in one week (`daily_online_offline.pkl`)

### Run Simulation 
At the beginning of each timestamp, the simulator performs the following:
1. Implement the station dynamics: i.e. making certain stations online or offline
2. Process rent demand: assign the orders within this timestamp to different EVs at the stations specified by the users
3. EV status update: keep tracking the status of each EV, if it is parked or running, its battery status, if its order has been completed, etc.
4. Process return demand: charge the EV at the destination stations, and handle cases such as if there isn't enough parking space for an arriving EV.

### Entry for Rebalancing Strategies
One could plug in their own rebalancing strategies in the following function: 

```
def rebalance_cars_across_hexagon(self, car, action, order_money=-1, average_dis=-1)
```

It is also possible to directly implement rebalancing strategies in the simulator. We leave the readers to explore further. 


### Output 
At each timestamp (currently set to 10 mins), a snapshot should be generated to show current status (including various statistics) of the simulation. The log will also be stored in ```logs/log.txt```. Here is a sample output:

```
[Snapshot] 2020-04-02 00:00:00 -- No rebalancing
+--------------------------------------------------+----------+-----------------------------------------------+------------+
|                       item                       |  amount  |                      item                     |   amount   |
+--------------------------------------------------+----------+-----------------------------------------------+------------+
|         Number of stations in operation          |   3014   |       Number of available charging docks      |   15161    |
|            Number of EVs in operation            |   8041   |           Number of EVs in charging           |    6966    |
|           Number of newly deployed EVs           |    15    |           Number of EVs on the road           |    1075    |
| Number of EVs returned with no available parking |   764    |  Number of EVs returned to an offline station |     8      |
|             Number of running orders             |   1075   |        Number of rebalancing operations       |     0      |
|            Number of finished orders             |  12093   |       Total number of unfulfilled orders      |    7392    |
|   Number of unfulfilled orders(EV battery low)   |   1958   | Number of unfulfilled orders(no available EV) |    5434    |
|                Total income (10k)                |  27.09   |      Total cost on user incentives (10k)      |    0.0     |
+--------------------------------------------------+----------+-----------------------------------------------+------------+
```

## Citations
```
@inproceedings{Luo:IJCAI:2020,
  title={Rebalancing Expanding EV Sharing Systems with Deep Reinforcement Learning},
  author={Luo, Man and Zhang, Wenzhe and Song, Tianyou and Li, Kun and Zhu, Hongming and Du, Bowen and Wen, Hongkai},
  booktitle={29th International Joint Conference on Artificial Intelligence (IJCAI)},
  year={2020}
}
```
