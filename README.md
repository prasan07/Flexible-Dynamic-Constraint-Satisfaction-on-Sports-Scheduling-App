import sys
from copy import deepcopy
import random

matchesPlayed = []
venueBias = 0

#method to check if the assigned state is consistent
def checkConsistency(match):
    global matchesPlayed
    if match in matchesPlayed:
        return False
    else:
        return True


def chooseNextHomeTeam(matchList, weekList, weekNo):
    global venueBias
    if(weekNo<venueBias):
        return weekList[weekNo][0]
    for team in weekList[weekNo]:
        for i in range(1, venueBias+1):
            for x, y in matchList[weekNo-i]:
                if y == team:
                    return team
    return -1

def chooseNextAwayTeam(matchList, teamUntested, weekNo):
    global venueBias
    if (weekNo < venueBias):
        return teamUntested[0]
    for team in teamUntested:
        for i in range(1, venueBias + 1):
            for x, y in matchList[weekNo - i]:
                if x == team:
                    return team
    return -1

def weeklyScheduler(matchList, weekList, weekNo):
    current = 0
    if len(weekList[weekNo]) == 0:
        return True
    #nextTeam = weekList[weekNo][0]
    nextTeam = chooseNextHomeTeam(matchList, weekList, weekNo)
    if nextTeam == -1:
        return False
    weekList[weekNo].remove(nextTeam)
    weekListcopy = deepcopy(weekList[weekNo])
    teamUntested = deepcopy(weekList[weekNo])
    while current < len(weekList[weekNo]):
        t = chooseNextAwayTeam(matchList, teamUntested, weekNo)
        if t == -1:
            return False;
        #t = weekList[weekNo][current]
        match = (nextTeam, t)
        if checkConsistency(match) == True:
            global matchesPlayed
            matchesPlayed.append(match)
            weekList[weekNo].remove(t)
            teamUntested.remove(t)
            matchList[weekNo].append(match)
            if weeklyScheduler(matchList, weekList, weekNo) == False:
                if match in matchesPlayed:
                    matchesPlayed.remove(match)
                if match in matchList[weekNo]:
                    matchList[weekNo].remove(match)
                match = ()
                del weekList[weekNo]
                weekList[weekNo] = deepcopy(weekListcopy)
            else:
                return True
        else:
            match=()
            teamUntested.remove(t)
        current+=1
    return False

def generateReverse(matchList, weekNo):
    currentList = matchList[weekNo]
    reverseList = []
    for match in currentList:
        reverseMatch = (match[1], match[0])
        reverseList.append(reverseMatch)
        global matchesPlayed
        matchesPlayed.append(reverseMatch)
    return reverseList

#main method to orchestrate the program solution
def main():
    teamCount = 6                       #teams count
    global venueBias
    venueBias = 2                       #home/Away consecutive allowed
    teamList = []                       #list of all teams
    matchList = {}                      #fixture schedule
    reverseSchedule = {}                #reverse fixture schedule
    weekList = {}                       #list of all teams for all weeks
    for i in range(0, teamCount-1):
        teamList = list(range(0,teamCount))
        #random.shuffle(teamList)
        weekList[i] = teamList
        matchList[i] = []
        weeklyScheduler(matchList, weekList, i)
        reverseSchedule[i] =[]
        reverseSchedule[i].append(generateReverse(matchList, i))
        print(matchList[i])
        #print(reverseSchedule[i])

if __name__ == "__main__":
    main()
