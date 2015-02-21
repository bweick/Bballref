#Script scrapes basic gamelogs for each player from bbref and organizes them into json where player name is key that returns a dataframe containing stats from all games 
from bs4 import BeautifulSoup
import urllib
import json
import pandas as pd
import string
from pandas import DataFrame

year = '2015'
filepath = 'C:\\Users\\Brian\\Documents\\Player Dictionaries\\NBA\\Basic Gamelogs\\BasicPlayers.json'
bblink = 'http://www.basketball-reference.com/'

# create list of links to players basic gamelogs by cycling through alphabetical structure on bbref website
names = []
for letter in string.ascii_lowercase:
    url = bblink + 'players/' + letter + '/'
    file_pointer = urllib.urlopen(url)
    soup = BeautifulSoup (file_pointer)
    
    #all active athletes have a bold font, so search for them with the 'strong' tag
    athletes = soup.findAll('strong')
    for n in athletes:
        holder = n.children.next()
        name_data = str(holder).replace(".html", "")
        name = name_data.split('>', 1)[1]
        name = name.split('<')[0]
        name = name.replace("'", "")
        link = name_data.split('"')[1]
        names.append((name, bblink + link + '/gamelog/'+ year + '/'))

players = {}
for name in names:
    name = str(name).strip('( )')
    player = str(name).split(',')[0]
    player = player.replace('\\', "")
    url1 = str(name).split(',')[1]
    players.update({player:url1})

#function which allows you to input dict and player and return an output        
def getPlayers(dic, player):
    return dic["'" + player + "'"]

#parses the tables on the bbref website and creates pandas dataframe out of them
def TableToFrame(table_soup, header):
    if not table_soup:
        return None
    else:
        rows = table_soup[0].findAll('tr')[1:]
        rows = [r for r in rows if len(r.findAll('td')) > 9]
        if len(rows) == 0:
            d = {}
            return DataFrame(d)
        else:
            parsed_table = [[col.getText() for col in row.findAll('td')] for row in rows]
            return pd.io.parsers.TextParser(parsed_table, names = header, index_col = 0, parse_dates = True).get_chunk()

player_data = {}

#create dataframe for each player using above function, header list is filled, also calculates and adds fantasy points column
for player in players:
    url = players[player]
    print player
    player = player.strip("'")
    url = url.strip(" '")
    file_pointer = urllib.urlopen(url)
    soup = BeautifulSoup(file_pointer)

    stats = soup.findAll('table', id = 'pgl_basic')
    
    if not stats:
        None
    else:
        header = []
        for th in stats[0].findAll('th'):
            if not th.getText() in header:
                header.append(th.getText())

        header[5] = u'HomeAway'
        header.insert(7, u'WinLoss')
        reg = TableToFrame(stats, header)
        if reg.empty:
            None
        else:
            reg['FP-FD'] = reg['PTS'] + reg['TRB']*1.2 + reg['AST']*1.5 + reg['STL']*2 + reg['BLK']*2 - reg['TOV']
        player_data.update({player:reg})

#cleans dataframe by getting rid of some extraneous data
clean_player = {}
for name in player_data:
    if player_data[name].empty:
        None
    else:
        df = player_data[name].drop(['G', 'Age', 'GS', 'GmSc', '+/-', 'FG', '3P', 'FT', 'PF'], 1)
    clean_player.update({name:df})

#convert dataframes into json and save as dictionary
jsplayer_data = {}
for player in clean_player:
    df = clean_player[player]
    jsdf = df.to_json()
    jsplayer_data.update({player:jsdf})

with open(filepath, 'wb') as fp:
    json.dump(jsplayer_data, fp)

print 'finished'
