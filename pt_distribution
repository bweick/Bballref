import json
import pandas as pd
from pandas import DataFrame

opfile = #location of dictionary you are pulling from
svfile = #location where you want csv to be saved

#Open json file and read it to a dict containing dataframes attached to player's names
json_file = open(opfile)
json_str = json_file.read()
json_data = json.loads(json_str)
replayer = {}
for key in json_data:
    player = json_data[key]
    df = pd.io.json.read_json(player)
    df = df.reindex(columns=['Date', 'Tm', 'Opp', 'HomeAway', 'MP', 'WinLoss', 'FGA', 'FG%', 'FTA', 'FT%', '3PA', '3P%', 'PTS', 'DRB', 'ORB', 'TRB', 'AST', 'STL', 'BLK', 'TOV', 'FP-FD'])
    df = df.sort(columns = 'Date')
    replayer.update({key:df})

#create table that displays the amount of time a player has scored within a certain fantasy point range
masterdist = DataFrame()
for player in replayer:
    rows = len(replayer[player].index)
    i = l15 = fiftw = twfive = tfthir = thirfv = tffor = forfv = fffif = fifty = 0
    for i in range(0, rows):
        sortnum = replayer[player].iloc[i]['FP-FD']
        if sortnum < 15: #logic to sort each game into each category
            l15 = l15 + 1
            i = i + 1
        elif 15 <= sortnum < 20:
            fiftw = fiftw + 1
            i = i + 1
        elif 20 <= sortnum < 25:
            twfive = twfive + 1
            i = i + 1
        elif 25 <= sortnum < 30:
            tfthir = tfthir + 1
            i = i + 1
        elif 30 <= sortnum < 35:
            thirfv = thirfv + 1
            i = i + 1
        elif 35 <= sortnum < 40:
            tffor = tffor + 1
            i = i + 1
        elif 40 <= sortnum < 45:
            forfv = forfv + 1
            i = i + 1
        elif 45 <= sortnum < 50:
            fffif = fffif + 1
            i = i + 1
        elif sortnum >= 50:
            fifty = fifty + 1
            i = i + 1
    individ = DataFrame({'Player':player, '< 15':l15, '15-20':fiftw, '20-25':twfive, '25-30':tfthir, '30-35':thirfv, '35-40':tffor, '40-45':forfv, '45-50':fffif, '> 50':fifty}, index=[0])
    masterdist = masterdist.append(individ) #create one frame containing all players
#format columns as desired, save as csv
masterdist = masterdist.reindex(columns=['Player', '< 15', '15-20', '20-25', '25-30', '30-35', '35-40', '40-45', '45-50', '> 50'])
masterdist.to_csv(svfile)
