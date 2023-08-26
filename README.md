## Exploring Hacker News Posts

[Hacker News](https://news.ycombinator.com/) is a site started by the startup incubator Y Combinator, where user-submitted stories (known as "posts") receive votes and comments, similar to reddit. Hacker News is extremely popular in technology and startup circles, and posts that make it to the top of the Hacker News listings can get hundreds of thousands of visitors as a result.

We're specifically interested in posts with titles that begin with either Ask HN or Show HN. Users submit Ask HN posts to ask the Hacker News community a specific question, or submit Show HN posts to showcase their work.

We'll compare these two types of posts to determine the following:

    Do Ask HN or Show HN receive more comments on average?
    Do posts created at a certain time receive more comments on average?

The dataset can be downloaded here.



```python
# importing the file
from csv import reader
opened_file = open('hacker_news.csv')
read_file = reader(opened_file)
hn = list(read_file)
headers = hn[0]
hn = hn[1:]

# custom function to display the list of lists neatly for easier readability
def print_nested_list(nested_list): 
    for inner_list in nested_list:
        print("\n")
        print(inner_list)

# print first 5 rows
print(headers)
print_nested_list(hn[:6])

```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    
    
    ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52']
    
    
    ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30']
    
    
    ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20']
    
    
    ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']
    
    
    ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']
    
    
    ['10482257', 'Title II kills investment? Comcast and other ISPs are now spending more', 'http://arstechnica.com/business/2015/10/comcast-and-other-isps-boost-network-investment-despite-net-neutrality/', '53', '22', 'Deinos', '10/31/2015 9:48']



```python
#empty lists that will soon be populated with it's corresponding posts
ask_posts = []
show_posts = []
other_posts = []

#loop through the entire dataset, adding it to the appropriate list category above
for row in hn:
    title = row[1]
    if title.lower().startswith('ask hn'): #"Ask HN" titles go in the ask_posts list above
        ask_posts.append(row)
    elif title.lower().startswith('show hn'):
        show_posts.append(row)
    else:
        other_posts.append(row)

#counting the number titles in each category
ask_count = len(ask_posts)
show_count = len(show_posts)
other_count = len(other_posts)

print(f"Number of 'Ask HN' rows is {ask_count}")
print(f"Number of 'Show HN' rows is {show_count}")
print(f"Number of all other rows is  {other_count}")


```

    Number of 'Ask HN' rows is 1744
    Number of 'Show HN' rows is 1162
    Number of all other rows is  17194



```python
#counting the total number of comments within the "Ask HN" list category
total_ask_comments = 0
for row in ask_posts: #for each row in the ask_posts list
    num_comments = int(row[4])
    total_ask_comments += num_comments #add the comment count to the total comment count

#finding the average number of comments
avg_ask_comments = total_ask_comments / ask_count

print("Average number of comments on an ask post is:",avg_ask_comments)

#same as the above code, but now for "Show HN" posts
total_show_comments = 0

for row in show_posts:
    num_comments = int(row[4])
    total_show_comments += num_comments
    
avg_show_comments = total_show_comments / show_count

print("Average number of comments on an show post is:",avg_show_comments)

```

    Average number of comments on an ask post is: 14.038417431192661
    Average number of comments on an show post is: 10.31669535283993


On average, "Ask HN" posts receive more comments than "Show HN" posts.


```python
import datetime as dt

result_list = [] 

for row in ask_posts: # adding the time and comment count of ASK HN posts to the above list
    created_at = row[6]
    comment_count = int(row[4])
    result_list.append([created_at, comment_count])

counts_by_hour = {} #number of ASK HN posts, in the hour
comments_by_hour = {} #total number of comments on every ASK HN post, in the hour

for row in result_list:
    date_st = row[0]
    date_dt = dt.datetime.strptime(date_st, "%m/%d/%Y %H:%M")
    hour = date_dt.strftime("%H")
    if hour not in counts_by_hour:
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = row[1]
    else:
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += row[1]

print(counts_by_hour)
print(comments_by_hour)

```

    {'09': 45, '13': 85, '10': 59, '14': 107, '16': 108, '23': 68, '12': 73, '17': 100, '15': 116, '21': 109, '20': 80, '02': 58, '18': 109, '03': 54, '05': 46, '19': 110, '01': 60, '22': 71, '08': 48, '04': 47, '00': 55, '06': 44, '07': 34, '11': 58}
    {'09': 251, '13': 1253, '10': 793, '14': 1416, '16': 1814, '23': 543, '12': 687, '17': 1146, '15': 4477, '21': 1745, '20': 1722, '02': 1381, '18': 1439, '03': 421, '05': 464, '19': 1188, '01': 683, '22': 479, '08': 492, '04': 337, '00': 447, '06': 397, '07': 267, '11': 641}



```python
avg_by_hour = [] # the average number of ASK HN comments per ASK HN post, within the hour

for hour in counts_by_hour:
    avg_by_hour.append([hour, comments_by_hour[hour] / counts_by_hour[hour], 2])

print(avg_by_hour)



```

    [['09', 5.5777777777777775, 2], ['13', 14.741176470588234, 2], ['10', 13.440677966101696, 2], ['14', 13.233644859813085, 2], ['16', 16.796296296296298, 2], ['23', 7.985294117647059, 2], ['12', 9.41095890410959, 2], ['17', 11.46, 2], ['15', 38.5948275862069, 2], ['21', 16.009174311926607, 2], ['20', 21.525, 2], ['02', 23.810344827586206, 2], ['18', 13.20183486238532, 2], ['03', 7.796296296296297, 2], ['05', 10.08695652173913, 2], ['19', 10.8, 2], ['01', 11.383333333333333, 2], ['22', 6.746478873239437, 2], ['08', 10.25, 2], ['04', 7.170212765957447, 2], ['00', 8.127272727272727, 2], ['06', 9.022727272727273, 2], ['07', 7.852941176470588, 2], ['11', 11.051724137931034, 2]]



```python
#code below serves to sort and find the top 5 hours by average number of comments of HN posts
swap_avg_by_hour = []

for row in avg_by_hour:
    swap_avg_by_hour.append([row[1], row[0]])
    
print(swap_avg_by_hour)

sorted_swap = sorted(swap_avg_by_hour, reverse=True)

print("Top 5 Hours for 'Ask HM' Posts Comments")

for row in sorted_swap[:5]:
    hour_dt = dt.datetime.strptime(row[1], "%H")
    hour = hour_dt.strftime("%H:%M")
    string = "{0}: {1:.2f} average comments per post".format(hour,row[0])
    print(string)
    
```

    [[5.5777777777777775, '09'], [14.741176470588234, '13'], [13.440677966101696, '10'], [13.233644859813085, '14'], [16.796296296296298, '16'], [7.985294117647059, '23'], [9.41095890410959, '12'], [11.46, '17'], [38.5948275862069, '15'], [16.009174311926607, '21'], [21.525, '20'], [23.810344827586206, '02'], [13.20183486238532, '18'], [7.796296296296297, '03'], [10.08695652173913, '05'], [10.8, '19'], [11.383333333333333, '01'], [6.746478873239437, '22'], [10.25, '08'], [7.170212765957447, '04'], [8.127272727272727, '00'], [9.022727272727273, '06'], [7.852941176470588, '07'], [11.051724137931034, '11']]
    Top 5 Hours for 'Ask HM' Posts Comments
    15:00: 38.59 average comments per post
    02:00: 23.81 average comments per post
    20:00: 21.52 average comments per post
    16:00: 16.80 average comments per post
    21:00: 16.01 average comments per post


# Conclusion


### Do Ask HN or Show HN receive more comments on average?

On average, "Ask HN" posts receive more comments than "Show HN" posts.

### Do posts created at a certain time receive more comments on average?

In order to receive a higher number of comments on an "Ask HN" post, it's best to post at the following  times:

15:00 
02:00 
20:00 
16:00 
21:00 
