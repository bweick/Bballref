#scrapes game info for each team and saves as json where team name key calls a dataframe filled with game by game data
from bs4 import BeautifulSoup
import urllib
import json
import pandas as pd

year = '2015'
filepath = #location you want to save json dictionary to
bblink = 'http://www.basketball-reference.com'

#fxn that creates dataframe for individ team data
def TeamTableToFrame(table_soup, header):
    if not table_soup:
        return None
    else:
        rows = table_soup[0].findAll('tr')[1:]
        rows = [r for r in rows if len(r.findAll('td')) > 0]
        parsed_table = [[col.getText() for col in row.findAll('td')] for row in rows]
        return pd.io.parsers.TextParser(parsed_table, names = header, parse_dates = True).get_chunk()


teamurl = bblink + '/teams/'
linkstore = urllib.urlopen(teamurl)
teamsoup = BeautifulSoup(linkstore)

#create dictionary containing url to team pages
teams = {}
teamlink = teamsoup.findAll('table', id = 'active')
for td in teamlink[0].findAll('td'):
    if td.findAll('a') == []:
        None
    else:
        tname = str(td).split('/')[2]
        tlink = str(td).split('"')[3]
        teams.update({tname:tlink})

#create a dictionary of dataframes associated with respective team
team_data = {}
full = pd.DataFrame()
for tname in teams:
    if year == '2015':
        if tname == 'CHA': #logic for 2015 season, due to difference between teams current 3-letter abbrev and url tag
            url = teamurl + 'CHO/' + year + '_games.html'
            tname = 'CHO'
        elif tname == 'NOH':
            url = teamurl + 'NOP/' + year + '_games.html'
            tname = 'NOP'
        elif tname == 'NJN':
            url = teamurl + 'BRK/' + year + '_games.html'
            tname = 'BRK'
        else:
            tlink = teams[tname]
            url = bblink + tlink + year + '_games.html'
    if year == '2014':
        if tname == 'NOH':
            url = teamurl + 'NOP/' + year + '_games.html'
            tname = 'NOP'
        elif tname == 'NJN':
            url = teamurl + 'BRK/' + year + '_games.html'
            tname = 'BRK'
        else:
            tlink = teams[tname]
            url = bblink + tlink + year + '_games.html'       
    print tname
    file_pointer = urllib.urlopen(url)
    soup = BeautifulSoup(file_pointer)

    links = soup.findAll('a', text='Box Score')
    for link in links:
        url1 = bblink + str(link['href'])
        date = url1.split('/')[4]
        date = date.split('.')[0]
        date = date[:-4]
        box_score = urllib.urlopen(url1)
        soup = BeautifulSoup (box_score)

        stats = soup.findAll('table', id = 'four_factors')

        header = []
        for th in stats[0].findAll('th')[3:]:
            if not th.getText() in header:
                header.append(th.getText())
        header[0] = u'Team'
        
        #separate opponents stats and desired teams stats, put opponent name back into dataframe, organize columns
        reg = TeamTableToFrame(stats, header)
        oppf = reg[reg.Team != tname]
        oppf = oppf.reset_index(drop=True)
        opp = oppf.get_value(0, 'Team')
        reg = reg[reg.Team == tname]
        reg['Date'] = date
        reg['Opp'] = str(opp)
        reg = reg.reindex(columns = ['Team', 'Date', 'Opp', 'Pace', 'eFG%', 'TOV%', 'ORB%', 'FT/FGA', 'ORtg'])
        # combine small dataframe from single game with additive dataframe of all games
        full = pd.DataFrame.append(reg, full, ignore_index = True)
    #add mean and st dev information for team at end of table
    mean = full.mean(axis = 0)
    std = full.std(axis = 0)
    full = pd.DataFrame.append(full, mean, ignore_index = True)
    full = pd.DataFrame.append(full, std, ignore_index = True)
    full = full.reset_index(drop=True)
    team_data.update({tname:full})
    full = pd.DataFrame()

#convert dataframe to json save as json dict
jsteam_data = {}
for team in team_data:
    df = team_data[team]
    jsdf = df.to_json()
    jsteam_data.update({team:jsdf})

with open(filepath + year + '.json', 'wb') as fp:
    json.dump(jsteam_data, fp)

print 'finished'
