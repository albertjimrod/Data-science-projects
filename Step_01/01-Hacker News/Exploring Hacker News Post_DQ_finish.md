![Y](https://s3.amazonaws.com/dq-content/354/hacker_news.jpg)

#  Exploring Hacker News Posts

## 1. Introduction

**Hacker News** (sometimes abbreviated as HN) is a social news website https://news.ycombinator.com/ focusing on computer science and entrepreneurship. 

It is run by Paul Graham's investment fund and startup incubator, Y Combinator. In general, content that can be submitted is defined as "anything that gratifies one's intellectual curiosity."

The word hacker in "Hacker News" is used in its original meaning and refers to the hacker culture which consists of people who enjoy tinkering with technology.

The intention was to recreate a community similar to the early days of Reddit. 

However, unlike Reddit where new users can immediately both upvote and downvote content, Hacker News does not allow users to downvote content until they have accumulated 501 "karma" points...

https://en.wikipedia.org/wiki/Hacker_News

We're specifically interested in posts with titles that begin with either `Ask HN` or `Show HN`. 

Users submit `Ask HN` posts to ask the Hacker News community a specific question and users also submit `Show HN` posts to show the Hacker News community a project, product, or just something interesting.

We'll compare these two types of posts to determine the following:

- Do `Ask HN` or `Show HN` receive more comments on average?

- Do posts created at a certain time receive more comments on average?


We will compare the most common types of posts (`Ask HN` or `Show HN`) in this site to know:


- What type of comments on average are the most abundant. 

- Analyze the relationship ( whether or not ) between the time 

- which posts are created and the number of comments they receive.

### Data dictionary:

- `id`: the unique identifier from Hacker News for the post

- `title`: the title of the post

- `url`: the URL that the posts links to, if the post has a URL

- `num_points`: the number of points the post acquired, calculated as the total number of upvotes minus the total number of downvotes

- `num_comments`: the number of comments on the post

- `author`: the username of the person who submitted the post

- `created_at`: the date and time of the post's submission


```python
from csv import reader
hn = open('hacker_news.csv')
hn = reader(hn)
hn = list(hn)
header = hn[0]
```

This is the header we are going to work with, which corresponds to row 0.


```python
header
```




    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']



These are the first 5 rows of our dataset with which we are going to work, so we can get an idea of the data content and what it looks like.


```python
for filas in hn[0:5]:
    print(filas)
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    ['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52']
    ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30']
    ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20']
    ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']


## 2. Managing headers on a list of lists

#### - 2.1 Extract the first row of data, and assign it to the variable headers.


```python
headers = hn[0]
```

#### - 2.2 Removing the first row from hn.


```python
del hn[0]
```

#### . 2.3 Display headers.


```python
print(headers)
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']


- 4.Display the first five rows of hn to verify that you removed the header row properly.


```python
print(hn[0:4])
```

    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01']]


## 3. Extracting Ask HN and Show HN Posts

Now that we have removed the `hn` headers, we are ready to filter our data.

Since we are only interested in the titles of entries that begin with:

- **`Ask HN`** or **`Show HN`** we will create new lists of lists containing only the data for those two titles.

To find posts starting with  **`Ask HN`** or **`Show HN`**, we will use the string method `startswith`. 

example:

Given an object of type string, say `string1`, we can check if it starts with **'whatever_it_is'**, just by inspecting the output of the object as follows:

- `string1.startswith('dq')`. 

If `string1` starts with **data**, it will return `True`, otherwise it will return `False`.

        print('dataquest'.startswith('Data'))
        `False`

        print('dataquest'.startswith('data'))
        `True`
        
In the above example we get `False` because dataquest does not start with `'what_it_is'`, however the second print out prints `True` because dataquest **does** start with `'what_it_is'`. 

**Case is important**, so **if we want to control case, we can use the lower method which returns a lowercase version of the initial string**. 

Here is an example:

`print('DataQuest'.lower())`

`dataquest`

### Filter strategy


The strategy we will follow next to filter the information we will create three empty lists called: 

- **`ask_posts`**

- **`show_posts`** 

- **`other_posts`**.


What we will do is convert the titles to lowercase with the method `.lower()` once this process is done, then what will be done is filter by the content we want with the method `object.startswith('string')`.

We will loop through `hn` and assign the title of each row to a variable called `title` which will be the one that already has the content in lowercase.

- If the lowercase version of the title starts with `ask hn`, add the row to `ask_posts`.

- If the lowercase version of the title starts with `show hn`, add the row to `show_posts`.

- Otherwise, add to `other_post`


```python
ask_posts = []
show_posts = []
other_posts = []
```


```python
header
```




    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']



A sample of what we will find in the position corresponding to the `fila[1]`.


```python
for fila in hn[7:15]:
    title = fila[1]
```

When the contents of `fila[1]` (**tittle**) are converted by the lower method and the title begins with lowercase version of title starting with `ask hn`, it will be append the row to `ask_posts`.

Else if the lowercase version of title starts with `show hn`, append the row to `show_posts`.

Otherwise append to `other_posts`.


```python
for fila in hn:
    title = fila[1]
    title = title.lower()   # all in lower_case
    
    if title.startswith('ask hn'):
        ask_posts.append(fila)
        
    elif title.startswith('show hn'):
        show_posts.append(fila)
        
    else:
        other_posts.append(fila)
```

- 4.Check the number of posts in:

- `ask_posts`

- `show_posts`

- `other_posts`


```python
len(ask_posts)
```




    1744




```python
len(show_posts)
```




    1162




```python
len(other_posts)
```




    17194



## 4. Calculating the Average Number of Comments for `Ask HN` and `Show HN` Posts

Samples of listing contents `ask_posts` and `show_posts`


```python
print(ask_posts[0:3])
```

    [['12296411', 'Ask HN: How to improve my personal website?', '', '2', '6', 'ahmedbaracat', '8/16/2016 9:55'], ['10610020', 'Ask HN: Am I the only one outraged by Twitter shutting down share counts?', '', '28', '29', 'tkfx', '11/22/2015 13:43'], ['11610310', 'Ask HN: Aby recent changes to CSS that broke mobile?', '', '1', '1', 'polskibus', '5/2/2016 10:14']]



```python
print(show_posts[0:3])
```

    [['10627194', 'Show HN: Wio Link  ESP8266 Based Web of Things Hardware Development Platform', 'https://iot.seeed.cc', '26', '22', 'kfihihc', '11/25/2015 14:03'], ['10646440', 'Show HN: Something pointless I made', 'http://dn.ht/picklecat/', '747', '102', 'dhotson', '11/29/2015 22:46'], ['11590768', 'Show HN: Shanhu.io, a programming playground powered by e8vm', 'https://shanhu.io', '1', '1', 'h8liu', '4/28/2016 18:05']]


The total number of comments on **ask** entries must be assigned to `total_ask_comments`.

Remenber Initialize `total_ask_comments` = 0.


```python
total_ask_comments = 0
```

- Using a for loop to iterating over the `ask_posts` entries.

- The `num_comments` column is the fifth column of `ask_posts`, so we will need to get the index element 4 in each row.

- Also convert the value to an integer in order to calculate the sum of all comments.
    - Add this value to `total_ask_comments`.
    - Calculate the average number of comments on ask entries and assign it to `avg_ask_comments`.
    
**Header looks like:**

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
       0      1       2          3             4             5           6

### Computing:

- #### avg number on `Ask post`
- ####  max of num_comments on `Ask post`.


```python
maxi = 0

for comment in ask_posts:
    total_ask_comments += int(comment[4])  # convert string to int ['num_comments']
    if maxi <= int(comment[4]):
        maxi = int(comment[4])
      
    
avg_ask_comments = total_ask_comments / len(ask_posts) # That`s the average: Ask comments / Total amount comments

print('avg number of "Ask_posts" comments:', round(avg_ask_comments,2))
print('max number of "Ask_posts" comment:', maxi)
```

    avg number of "Ask_posts" comments: 14.04
    max number of "Ask_posts" comment: 947


- ### Title of the max of `num_comments` on `Ask` post.

**Header looks like:**

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
       0      1       2          3             4             5           6


```python
# ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']

most_important_questions = {}
maxi = 0

for comment in ask_posts:
     if maxi < int(comment[4]): #Only if comment[4] is bigger than maxi, this value will be on dictionary. 
            maxi = int(comment[4]) # update the value 
            # save into dictionary: key:num_comment value: title
            most_important_questions[int(comment[4])] = comment[1] # ['title']
            
most_voted = list(reversed(sorted(most_important_questions.keys()))) # list of most_important_questions 
                                                                     # by keys sorted 

print("-- Score --            -- Tittle -- ")

for score in most_voted:
    texto = "    {points}     {relevance}".format(points = score,relevance = most_important_questions[score])
    print(texto)
```

    -- Score --            -- Tittle -- 
        947     Ask HN: Who is hiring? (August 2016)
        910     Ask HN: Who is hiring? (September 2016)
        266     Ask HN: What are the must-read books about economics/finance?
        250     Ask HN: Who wants to be hired? (June 2016)
        234     Ask HN: What are you currently building?
        182     Ask HN: What is your go-to example for a good REST API?
        37     Ask HN: Things you created in 2015?
        33     Ask HN: teaching basic coding and web design offline, solely via iOS devices?
        29     Ask HN: Am I the only one outraged by Twitter shutting down share counts?
        6     Ask HN: How to improve my personal website?


### Computing:

- #### avg number on `Show post`

- #### max of `num_comments` on Show post.


```python
show_posts[:2] # Sample of content.
```




    [['10627194',
      'Show HN: Wio Link  ESP8266 Based Web of Things Hardware Development Platform',
      'https://iot.seeed.cc',
      '26',
      '22',
      'kfihihc',
      '11/25/2015 14:03'],
     ['10646440',
      'Show HN: Something pointless I made',
      'http://dn.ht/picklecat/',
      '747',
      '102',
      'dhotson',
      '11/29/2015 22:46']]




```python
# ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']

max_show = 0
total_show_comments = 0

for row in show_posts:
    total_show_comments += int(row[4]) ## Accumulator value
    if max_show <= int(row[4]):
        max_show = int(row[4])
    
avg_show_comments = total_show_comments / len(show_posts)
print('avg number of comments: "Show posts" ', round(avg_show_comments,2))

print('max number of "Show posts" comments:', max_show)
```

    avg number of comments: "Show posts"  10.32
    max number of "Show posts" comments: 306


- ### Title of the max of `num_comments` on `Show post`.


```python
most_important_show = {}
max_show = 0

for comment in show_posts:
     if max_show < int(comment[4]):
            max_show = int(comment[4])
            most_important_show[int(comment[4])] = comment[1]
            
most_voted_show = list(reversed(sorted(most_important_show.keys())))

print("-- Score --    -- Tittle -- ")

for score in most_voted_show:
    texto = "   {points}           {relevance}".format(points = score,relevance = most_important_show[score])
    print(texto)
```

    -- Score --    -- Tittle -- 
       306           Show HN: BitKeeper  Enterprise-ready version control, now open-source
       168           Show HN: Nodal. Next-Generation Node.js Server and Framework
       134           Show HN: Download any song without knowing its name
       102           Show HN: Something pointless I made
       22           Show HN: Wio Link  ESP8266 Based Web of Things Hardware Development Platform



```python
%%html
<style>
table {float:left}
</style>
```


<style>
table {float:left}
</style>



### Observations

- #### avg number of "Ask_posts" comments: 14.04
- #### max number of "Ask_posts" comment: 947


|-- Score --|    -- Tittle --| 
|---|---| 
    |947 |    Ask HN: Who is hiring? (August 2016)|
    |910 |    Ask HN: Who is hiring? (September 2016)|
    |266 |    Ask HN: What are the must-read books about economics/finance?|
    |250 |    Ask HN: Who wants to be hired? (June 2016)|
    |234 |    Ask HN: What are you currently building?|
    |182 |    Ask HN: What is your go-to example for a good REST API?|
    |37  |   Ask HN: Things you created in 2015?|
    |33  |   Ask HN: teaching basic coding and web design offline, solely via iOS devices?|
    |29  |   Ask HN: Am I the only one outraged by Twitter shutting down share counts?|
    |6   |  Ask HN: How to improve my personal website?|

- #### avg number of `"Show posts"` comments: 10.32
- #### max number of `"Show posts"` comments: 306


|-- Score --|    -- Tittle --| 
|---|---|
|306|           Show HN: BitKeeper  Enterprise-ready version control, now open-source|
|168|           Show HN: Nodal. Next-Generation Node.js Server and Framework|
|134|           Show HN: Download any song without knowing its name|
|102|           Show HN: Something pointless I made|
|22 |          Show HN: Wio Link  ESP8266 Based Web of Things Hardware Development Platform|
   


Clearly there is a higher average value in the creation of questions we also see that the scores that have received the questions are much higher than the posts with a show content.

The highest score in show post is almost close to the fifth position of the posts in which questions are asked.

We can observe is that what matters most to the users of this forum (always taking into account the value of the scores) and that it is in the first three positions is to know who or what company is hiring, so this is a place to know about this. 

Another thing that we can deduce is that in June 2016 the importance of that question occupies the first position while as time passes in August and September the importance (according to the score) is reduced.


In another hand the relation to the content of the show post the most voted is related to know that BitKeeper is a fast, enterprise-ready, available as Open Source under the Apache 2.0 License distributed SCM that scales up to very large projects and down to tiny ones.

## 5. Finding the Number of Ask posted and Comments posted by Hour Created


Ask posts are more likely to receive comments, **we'll focus our analysis just on these posts.**

**We'll determine if `ask posts` created at a certain time are more likely to attract comments**. We'll use the following steps to perform this analysis:

- Calculate the number of **ask posts created in each hour of the day, along with the number of comments received.

- Calculate the **average number of comments ask posts receive by hour created**.

**NOTE**: we can use the `datetime.strptime()` constructor to **parse dates stored as strings and return datetime objects**, example:

   - `date_1_str = "December 24, 1984"`
   - `date_1_dt = dt.datetime.strptime(date_1_str, "%B %d, %Y")`

Let's use this technique to calculate the number of ask posts created per hour, along with the total number of comments.


```python
import datetime as dt
```

Create an empty list, and assign it to `result_list`. This will be a list of lists.


```python
result_list = []
```

Loop over `ask_posts`, and append to `result_list` a list with two elements:

   - The first element should be the column `created_at`. 
   
Because the `created_at` column is the seventh column in `ask_posts`, you'll need to get the element at index 6 in each row.
    
   - The second element should be the number of comments of the post. You'll also need to convert the value to an integer.
   
**Header looks like:**

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
       0      1       2          3             4             5           6


```python
for row in ask_posts:
    time_stamp = row[6]
    num_comment = row[4]
    tupla = (time_stamp, num_comment)
    result_list.append(tupla)

result_list
```




    [('8/16/2016 9:55', '6'),
     ('11/22/2015 13:43', '29'),
     ('5/2/2016 10:14', '1'),
     ('8/2/2016 14:20', '3'),
     ('10/15/2015 16:38', '17'),
     ('9/26/2015 23:23', '1'),
     ('4/22/2016 12:24', '4'),
     ('11/16/2015 9:22', '1'),
     ('2/24/2016 17:57', '1'),
     ('6/4/2016 17:17', '2'),
     ('9/19/2015 17:04', '7'),
     ('9/22/2015 13:16', '1'),
     ('6/21/2016 15:45', '1'),
     ('1/13/2016 21:17', '4'),
     ('10/4/2015 21:27', '4'),
     ('1/25/2016 20:27', '2'),
     ('10/27/2015 2:47', '3'),
     ('1/19/2016 12:01', '1'),
     ('3/22/2016 2:05', '22'),
     ('9/8/2015 14:04', '2'),
     ('8/28/2016 18:06', '2'),
     ('7/20/2016 13:44', '7'),
     ('9/12/2016 16:52', '7'),
     ('2/29/2016 17:52', '3'),
     ('4/18/2016 15:28', '6'),
     ('12/28/2015 14:38', '2'),
     ('4/4/2016 3:34', '1'),
     ('1/15/2016 21:47', '3'),
     ('11/19/2015 5:33', '29'),
     ('12/20/2015 3:59', '2'),
     ('10/15/2015 21:34', '20'),
     ('2/26/2016 19:20', '3'),
     ('8/2/2016 18:00', '3'),
     ('2/28/2016 1:24', '33'),
     ('1/13/2016 9:12', '5'),
     ('5/6/2016 1:14', '4'),
     ('6/23/2016 13:59', '7'),
     ('4/30/2016 17:21', '11'),
     ('10/20/2015 19:21', '1'),
     ('10/25/2015 15:09', '9'),
     ('5/4/2016 14:14', '2'),
     ('12/23/2015 20:48', '37'),
     ('6/1/2016 16:19', '1'),
     ('7/10/2016 22:19', '2'),
     ('6/21/2016 23:40', '4'),
     ('4/24/2016 19:36', '1'),
     ('5/20/2016 1:26', '1'),
     ('6/24/2016 17:02', '182'),
     ('7/12/2016 19:34', '5'),
     ('10/16/2015 16:36', '9'),
     ('3/15/2016 22:15', '8'),
     ('11/24/2015 1:05', '24'),
     ('5/30/2016 8:14', '5'),
     ('1/11/2016 4:53', '3'),
     ('8/28/2016 20:38', '4'),
     ('3/25/2016 12:52', '7'),
     ('4/14/2016 0:58', '10'),
     ('12/31/2015 14:18', '3'),
     ('2/26/2016 10:23', '3'),
     ('4/1/2016 16:24', '4'),
     ('6/22/2016 21:15', '3'),
     ('10/13/2015 17:07', '20'),
     ('3/3/2016 10:40', '1'),
     ('4/3/2016 16:57', '140'),
     ('3/14/2016 13:42', '5'),
     ('5/2/2016 13:12', '3'),
     ('9/21/2016 2:36', '30'),
     ('10/22/2015 16:46', '2'),
     ('6/12/2016 19:28', '2'),
     ('12/23/2015 14:40', '5'),
     ('3/4/2016 14:13', '1'),
     ('8/18/2016 14:33', '17'),
     ('12/31/2015 6:14', '1'),
     ('8/11/2016 17:59', '1'),
     ('1/30/2016 23:37', '5'),
     ('12/10/2015 18:49', '5'),
     ('12/3/2015 5:50', '2'),
     ('2/19/2016 17:11', '8'),
     ('4/19/2016 20:04', '2'),
     ('2/15/2016 18:16', '6'),
     ('11/11/2015 14:29', '22'),
     ('12/8/2015 2:30', '7'),
     ('9/29/2015 20:46', '12'),
     ('3/29/2016 0:15', '1'),
     ('12/15/2015 23:26', '2'),
     ('11/21/2015 15:42', '72'),
     ('11/22/2015 17:27', '2'),
     ('6/15/2016 8:52', '130'),
     ('9/15/2015 16:52', '3'),
     ('12/26/2015 3:46', '2'),
     ('10/16/2015 19:37', '7'),
     ('3/7/2016 18:02', '15'),
     ('1/4/2016 7:06', '2'),
     ('12/30/2015 19:03', '6'),
     ('10/13/2015 17:26', '1'),
     ('2/2/2016 12:47', '1'),
     ('11/13/2015 16:57', '10'),
     ('4/11/2016 14:17', '2'),
     ('10/30/2015 2:34', '15'),
     ('5/8/2016 8:30', '8'),
     ('10/30/2015 21:12', '2'),
     ('9/24/2016 14:03', '1'),
     ('8/20/2016 23:49', '1'),
     ('12/30/2015 15:30', '1'),
     ('8/16/2016 18:05', '6'),
     ('8/9/2016 1:30', '43'),
     ('4/22/2016 22:31', '19'),
     ('1/21/2016 14:38', '5'),
     ('6/30/2016 21:22', '1'),
     ('1/3/2016 19:37', '234'),
     ('10/10/2015 17:12', '5'),
     ('12/28/2015 18:47', '6'),
     ('3/3/2016 16:38', '9'),
     ('1/22/2016 14:44', '1'),
     ('5/27/2016 6:27', '1'),
     ('6/23/2016 10:57', '19'),
     ('4/13/2016 7:06', '3'),
     ('9/25/2015 18:32', '1'),
     ('9/10/2016 21:18', '25'),
     ('6/15/2016 18:33', '7'),
     ('9/7/2016 21:16', '7'),
     ('12/16/2015 10:01', '71'),
     ('7/11/2016 19:22', '3'),
     ('6/21/2016 16:44', '17'),
     ('3/18/2016 15:16', '2'),
     ('12/1/2015 15:01', '5'),
     ('12/28/2015 11:42', '2'),
     ('10/19/2015 0:48', '3'),
     ('9/19/2016 0:16', '61'),
     ('3/3/2016 18:51', '2'),
     ('11/5/2015 12:44', '17'),
     ('10/19/2015 5:27', '2'),
     ('2/21/2016 19:54', '3'),
     ('7/18/2016 16:59', '2'),
     ('8/29/2016 0:07', '1'),
     ('4/12/2016 3:38', '5'),
     ('2/20/2016 21:53', '1'),
     ('6/15/2016 19:27', '13'),
     ('2/11/2016 10:48', '12'),
     ('9/13/2015 10:21', '1'),
     ('8/26/2016 14:28', '1'),
     ('9/16/2016 19:28', '185'),
     ('1/29/2016 23:32', '7'),
     ('5/19/2016 15:59', '2'),
     ('11/26/2015 22:51', '4'),
     ('11/9/2015 11:28', '2'),
     ('4/21/2016 20:41', '2'),
     ('8/22/2016 14:38', '1'),
     ('8/23/2016 13:13', '2'),
     ('11/20/2015 6:14', '2'),
     ('5/11/2016 21:08', '2'),
     ('1/16/2016 21:25', '1'),
     ('10/13/2015 10:42', '22'),
     ('12/14/2015 4:35', '37'),
     ('9/13/2016 11:47', '8'),
     ('11/13/2015 15:12', '1'),
     ('3/16/2016 13:20', '55'),
     ('11/24/2015 22:27', '9'),
     ('9/4/2016 12:26', '3'),
     ('2/4/2016 18:44', '20'),
     ('3/14/2016 16:57', '6'),
     ('10/4/2015 15:50', '8'),
     ('4/13/2016 6:01', '3'),
     ('3/21/2016 12:44', '10'),
     ('3/17/2016 3:39', '11'),
     ('5/31/2016 2:41', '1'),
     ('10/1/2015 2:02', '8'),
     ('11/17/2015 0:07', '2'),
     ('9/8/2016 12:58', '35'),
     ('6/24/2016 14:29', '4'),
     ('2/11/2016 17:40', '10'),
     ('2/8/2016 22:09', '3'),
     ('9/19/2015 12:54', '8'),
     ('4/26/2016 19:42', '1'),
     ('1/1/2016 17:05', '55'),
     ('3/11/2016 16:48', '8'),
     ('6/5/2016 11:08', '5'),
     ('12/17/2015 15:33', '1'),
     ('4/13/2016 18:20', '4'),
     ('4/1/2016 13:56', '4'),
     ('8/14/2016 19:15', '3'),
     ('10/17/2015 22:15', '1'),
     ('11/13/2015 19:07', '2'),
     ('8/12/2016 18:23', '11'),
     ('5/7/2016 10:30', '1'),
     ('12/15/2015 8:30', '3'),
     ('6/29/2016 1:54', '3'),
     ('7/15/2016 13:34', '20'),
     ('4/8/2016 19:49', '1'),
     ('1/28/2016 6:20', '9'),
     ('8/23/2016 17:58', '5'),
     ('6/1/2016 15:01', '250'),
     ('3/13/2016 22:09', '11'),
     ('12/1/2015 15:00', '93'),
     ('7/4/2016 19:40', '5'),
     ('9/20/2015 12:41', '2'),
     ('5/13/2016 1:14', '1'),
     ('1/27/2016 20:31', '4'),
     ('2/4/2016 8:03', '4'),
     ('6/22/2016 16:55', '2'),
     ('6/18/2016 16:35', '2'),
     ('3/17/2016 13:43', '92'),
     ('8/27/2016 11:44', '4'),
     ('5/15/2016 16:13', '112'),
     ('5/5/2016 4:51', '4'),
     ('5/5/2016 13:13', '16'),
     ('7/14/2016 0:31', '4'),
     ('2/22/2016 1:52', '3'),
     ('1/7/2016 12:06', '4'),
     ('5/7/2016 2:33', '4'),
     ('4/13/2016 2:53', '4'),
     ('6/21/2016 21:59', '9'),
     ('9/19/2015 18:22', '11'),
     ('5/26/2016 0:05', '43'),
     ('11/14/2015 22:02', '3'),
     ('7/27/2016 7:38', '1'),
     ('3/26/2016 19:54', '3'),
     ('5/11/2016 16:50', '5'),
     ('1/4/2016 8:21', '2'),
     ('7/13/2016 22:50', '1'),
     ('5/16/2016 4:18', '1'),
     ('11/4/2015 10:45', '3'),
     ('1/17/2016 15:48', '3'),
     ('4/2/2016 8:56', '29'),
     ('10/31/2015 7:10', '11'),
     ('3/15/2016 14:00', '3'),
     ('3/26/2016 15:23', '6'),
     ('9/11/2015 15:03', '3'),
     ('5/17/2016 4:47', '3'),
     ('4/18/2016 13:22', '15'),
     ('1/28/2016 23:14', '1'),
     ('2/1/2016 21:29', '1'),
     ('12/21/2015 7:36', '2'),
     ('7/28/2016 13:54', '5'),
     ('12/10/2015 17:10', '9'),
     ('7/13/2016 3:25', '1'),
     ('2/3/2016 19:55', '2'),
     ('6/13/2016 21:27', '7'),
     ('9/21/2016 4:02', '1'),
     ('1/12/2016 21:45', '1'),
     ('7/14/2016 19:28', '5'),
     ('6/18/2016 14:13', '11'),
     ('5/13/2016 16:30', '13'),
     ('9/19/2016 15:31', '2'),
     ('5/9/2016 23:02', '2'),
     ('8/17/2016 21:38', '3'),
     ('7/1/2016 15:03', '2'),
     ('5/22/2016 20:44', '1'),
     ('9/28/2015 16:51', '3'),
     ('9/23/2015 19:38', '5'),
     ('7/23/2016 8:37', '1'),
     ('3/5/2016 17:53', '2'),
     ('4/21/2016 0:33', '32'),
     ('10/20/2015 18:13', '28'),
     ('6/29/2016 17:36', '5'),
     ('1/11/2016 23:52', '2'),
     ('10/10/2015 23:53', '1'),
     ('2/8/2016 4:33', '2'),
     ('10/3/2015 6:44', '6'),
     ('8/7/2016 14:20', '10'),
     ('11/4/2015 19:59', '3'),
     ('1/26/2016 21:39', '3'),
     ('2/29/2016 7:55', '2'),
     ('5/10/2016 20:12', '1'),
     ('2/29/2016 16:14', '2'),
     ('3/7/2016 8:32', '1'),
     ('12/11/2015 5:09', '10'),
     ('12/24/2015 21:13', '2'),
     ('8/15/2016 23:19', '5'),
     ('8/28/2016 18:58', '3'),
     ('10/21/2015 20:48', '1'),
     ('6/16/2016 15:06', '1'),
     ('9/10/2016 16:01', '5'),
     ('2/3/2016 9:30', '60'),
     ('1/19/2016 0:20', '2'),
     ('11/30/2015 17:19', '1'),
     ('6/16/2016 8:42', '6'),
     ('8/11/2016 17:40', '12'),
     ('5/25/2016 15:46', '22'),
     ('10/3/2015 18:03', '32'),
     ('9/20/2015 17:27', '62'),
     ('3/23/2016 13:49', '2'),
     ('10/26/2015 20:44', '2'),
     ('7/18/2016 3:09', '18'),
     ('9/8/2016 18:20', '2'),
     ('11/24/2015 19:21', '1'),
     ('12/21/2015 10:43', '3'),
     ('12/19/2015 2:17', '11'),
     ('5/18/2016 19:03', '4'),
     ('11/25/2015 2:21', '11'),
     ('9/13/2015 13:31', '34'),
     ('8/28/2016 5:19', '5'),
     ('11/25/2015 18:40', '10'),
     ('1/16/2016 23:24', '2'),
     ('10/9/2015 15:03', '6'),
     ('10/25/2015 12:42', '12'),
     ('5/23/2016 19:41', '2'),
     ('9/21/2016 8:26', '1'),
     ('8/8/2016 5:41', '3'),
     ('9/22/2016 11:52', '266'),
     ('7/12/2016 20:30', '183'),
     ('9/22/2016 12:50', '3'),
     ('2/17/2016 21:12', '10'),
     ('7/28/2016 16:16', '2'),
     ('4/26/2016 19:29', '4'),
     ('1/25/2016 18:41', '1'),
     ('5/21/2016 4:26', '5'),
     ('11/8/2015 20:26', '8'),
     ('4/25/2016 18:30', '1'),
     ('4/14/2016 13:24', '5'),
     ('2/8/2016 21:50', '10'),
     ('6/15/2016 15:15', '3'),
     ('5/4/2016 4:42', '1'),
     ('3/23/2016 15:58', '1'),
     ('8/1/2016 17:16', '2'),
     ('9/15/2016 13:50', '1'),
     ('2/14/2016 15:57', '6'),
     ('6/14/2016 22:32', '1'),
     ('9/22/2016 15:44', '1'),
     ('1/7/2016 14:00', '1'),
     ('10/7/2015 18:09', '6'),
     ('4/12/2016 4:22', '2'),
     ('11/12/2015 15:38', '12'),
     ('12/17/2015 5:37', '1'),
     ('11/12/2015 16:28', '2'),
     ('9/19/2016 18:51', '14'),
     ('4/8/2016 17:54', '2'),
     ('11/30/2015 22:15', '6'),
     ('4/27/2016 13:34', '10'),
     ('12/10/2015 1:52', '19'),
     ('7/3/2016 20:14', '1'),
     ('5/5/2016 22:38', '7'),
     ('7/16/2016 2:23', '8'),
     ('10/10/2015 13:53', '4'),
     ('2/4/2016 11:18', '5'),
     ('12/5/2015 4:41', '18'),
     ('8/6/2016 18:34', '17'),
     ('8/5/2016 10:05', '7'),
     ('9/21/2015 20:55', '3'),
     ('9/10/2015 14:22', '1'),
     ('2/4/2016 5:46', '10'),
     ('11/9/2015 17:24', '4'),
     ('12/29/2015 11:51', '3'),
     ('11/10/2015 21:30', '1'),
     ('4/20/2016 3:46', '2'),
     ('11/12/2015 16:43', '9'),
     ('6/5/2016 11:55', '2'),
     ('9/20/2016 14:42', '4'),
     ('10/24/2015 20:58', '26'),
     ('11/7/2015 14:27', '11'),
     ('1/16/2016 3:26', '15'),
     ('6/26/2016 6:37', '7'),
     ('12/6/2015 19:19', '46'),
     ('5/29/2016 6:50', '5'),
     ('2/5/2016 17:38', '5'),
     ('9/19/2015 15:38', '3'),
     ('9/15/2016 7:11', '3'),
     ('1/29/2016 3:26', '16'),
     ('4/23/2016 12:37', '3'),
     ('9/28/2015 0:35', '8'),
     ('1/11/2016 13:05', '4'),
     ('8/26/2016 8:39', '24'),
     ('8/23/2016 11:50', '8'),
     ('9/22/2015 11:37', '4'),
     ('4/21/2016 8:43', '1'),
     ('8/6/2016 12:25', '7'),
     ('6/22/2016 15:44', '2'),
     ('4/29/2016 11:54', '1'),
     ('9/13/2015 11:25', '4'),
     ('6/11/2016 22:51', '2'),
     ('11/6/2015 5:13', '22'),
     ('6/2/2016 9:25', '1'),
     ('9/17/2015 3:28', '9'),
     ('5/5/2016 14:45', '16'),
     ('5/2/2016 11:06', '1'),
     ('6/13/2016 12:03', '2'),
     ('1/26/2016 22:57', '43'),
     ('9/22/2015 18:23', '3'),
     ('11/10/2015 19:04', '18'),
     ('2/24/2016 18:55', '3'),
     ('1/28/2016 1:03', '22'),
     ('5/15/2016 8:53', '4'),
     ('5/30/2016 12:16', '5'),
     ('6/11/2016 16:24', '2'),
     ('12/1/2015 20:07', '4'),
     ('11/3/2015 1:26', '13'),
     ('4/25/2016 19:20', '1'),
     ('7/18/2016 17:51', '1'),
     ('3/31/2016 17:07', '29'),
     ('11/12/2015 12:26', '2'),
     ('10/20/2015 2:24', '41'),
     ('9/6/2015 14:53', '6'),
     ('5/6/2016 14:58', '4'),
     ('12/10/2015 18:16', '1'),
     ('7/20/2016 14:06', '2'),
     ('1/13/2016 13:26', '6'),
     ('2/8/2016 15:22', '3'),
     ('8/25/2016 15:31', '10'),
     ('2/11/2016 21:32', '1'),
     ('7/1/2016 18:53', '2'),
     ('4/8/2016 5:57', '3'),
     ('4/24/2016 21:43', '9'),
     ('10/29/2015 3:20', '14'),
     ('9/6/2016 15:26', '1'),
     ('5/1/2016 9:39', '2'),
     ('12/22/2015 20:48', '2'),
     ('8/10/2016 12:57', '1'),
     ('2/18/2016 15:09', '1'),
     ('5/21/2016 18:52', '11'),
     ('1/15/2016 18:13', '1'),
     ('3/24/2016 1:50', '9'),
     ('3/15/2016 22:13', '4'),
     ('6/21/2016 14:35', '85'),
     ('6/21/2016 15:14', '4'),
     ('8/13/2016 9:33', '42'),
     ('10/3/2015 15:55', '7'),
     ('3/8/2016 22:05', '9'),
     ('8/26/2016 0:49', '1'),
     ('5/28/2016 11:40', '4'),
     ('4/18/2016 18:36', '9'),
     ('1/14/2016 10:58', '1'),
     ('8/10/2016 22:24', '20'),
     ('8/31/2016 11:41', '6'),
     ('10/14/2015 23:09', '1'),
     ('1/21/2016 22:42', '2'),
     ('9/1/2016 15:00', '910'),
     ('2/4/2016 22:43', '3'),
     ('9/5/2016 12:34', '1'),
     ('7/20/2016 10:22', '12'),
     ('11/5/2015 4:13', '2'),
     ('2/26/2016 1:52', '3'),
     ('2/16/2016 19:49', '5'),
     ('10/28/2015 2:34', '12'),
     ('1/14/2016 0:29', '3'),
     ('5/31/2016 17:23', '1'),
     ('8/9/2016 13:40', '95'),
     ('12/16/2015 2:50', '6'),
     ('11/19/2015 3:54', '1'),
     ('7/29/2016 13:58', '1'),
     ('6/25/2016 15:01', '3'),
     ('10/7/2015 0:15', '9'),
     ('12/26/2015 18:09', '66'),
     ('1/18/2016 21:37', '1'),
     ('7/9/2016 14:49', '3'),
     ('7/9/2016 23:37', '8'),
     ('4/8/2016 20:20', '2'),
     ('8/21/2016 17:30', '6'),
     ('4/20/2016 21:38', '10'),
     ('10/12/2015 21:46', '6'),
     ('12/25/2015 3:25', '2'),
     ('4/11/2016 22:16', '6'),
     ('9/13/2016 6:25', '2'),
     ('11/24/2015 4:26', '11'),
     ('7/16/2016 15:01', '2'),
     ('3/23/2016 0:38', '29'),
     ('5/13/2016 14:45', '7'),
     ('9/21/2015 14:57', '12'),
     ('5/25/2016 14:16', '2'),
     ('7/22/2016 11:16', '2'),
     ('9/26/2015 20:33', '6'),
     ('1/26/2016 16:04', '1'),
     ('7/14/2016 6:31', '2'),
     ('1/3/2016 18:57', '7'),
     ('5/20/2016 10:39', '5'),
     ('4/7/2016 22:55', '1'),
     ('4/20/2016 5:08', '6'),
     ('9/13/2016 1:58', '1'),
     ('10/1/2015 11:38', '1'),
     ('2/3/2016 16:46', '2'),
     ('5/28/2016 14:53', '125'),
     ('11/10/2015 4:52', '1'),
     ('10/5/2015 15:35', '6'),
     ('9/16/2016 21:19', '2'),
     ('2/25/2016 2:57', '5'),
     ('7/19/2016 8:52', '1'),
     ('2/27/2016 13:17', '17'),
     ('1/14/2016 22:42', '2'),
     ('5/14/2016 14:06', '4'),
     ('7/13/2016 3:25', '3'),
     ('5/2/2016 22:54', '13'),
     ('4/6/2016 3:19', '2'),
     ('5/3/2016 17:24', '2'),
     ('1/13/2016 16:40', '2'),
     ('6/25/2016 15:35', '7'),
     ('7/22/2016 6:34', '2'),
     ('7/23/2016 21:15', '1'),
     ('2/19/2016 20:59', '40'),
     ('8/31/2016 19:56', '3'),
     ('4/6/2016 5:41', '1'),
     ('4/28/2016 16:37', '51'),
     ('6/8/2016 19:43', '12'),
     ('5/15/2016 11:35', '6'),
     ('4/12/2016 16:49', '1'),
     ('12/22/2015 14:42', '14'),
     ('7/23/2016 6:26', '10'),
     ('8/18/2016 20:12', '6'),
     ('2/26/2016 8:58', '2'),
     ('2/16/2016 17:34', '31'),
     ('3/20/2016 22:05', '6'),
     ('2/1/2016 18:42', '2'),
     ('1/18/2016 15:27', '162'),
     ('9/7/2016 18:33', '2'),
     ('9/3/2016 17:25', '6'),
     ('1/24/2016 20:29', '7'),
     ('12/19/2015 19:01', '2'),
     ('4/2/2016 10:47', '4'),
     ('9/22/2016 6:25', '1'),
     ('8/22/2016 19:28', '9'),
     ('2/3/2016 23:43', '15'),
     ('9/11/2015 0:07', '3'),
     ('12/1/2015 1:24', '14'),
     ('8/4/2016 23:45', '1'),
     ('8/9/2016 17:50', '2'),
     ('1/6/2016 15:49', '3'),
     ('5/14/2016 6:20', '2'),
     ('6/20/2016 23:43', '12'),
     ('3/9/2016 4:44', '2'),
     ('12/2/2015 16:20', '5'),
     ('9/24/2015 22:38', '1'),
     ('8/30/2016 2:44', '1'),
     ('6/16/2016 1:59', '4'),
     ('3/4/2016 21:35', '2'),
     ('8/17/2016 12:12', '69'),
     ('3/9/2016 22:51', '2'),
     ('4/26/2016 6:42', '2'),
     ('11/18/2015 22:15', '1'),
     ('12/22/2015 10:55', '6'),
     ('8/16/2016 17:33', '5'),
     ('5/2/2016 23:49', '7'),
     ('10/30/2015 18:28', '5'),
     ('1/3/2016 21:14', '3'),
     ('11/26/2015 9:55', '3'),
     ('9/14/2015 14:34', '1'),
     ('8/5/2016 0:21', '3'),
     ('10/16/2015 17:54', '4'),
     ('10/13/2015 2:36', '15'),
     ('10/1/2015 10:40', '1'),
     ('6/5/2016 6:29', '1'),
     ('9/7/2016 11:02', '6'),
     ('10/13/2015 18:48', '3'),
     ('8/21/2016 15:03', '4'),
     ('4/29/2016 21:24', '2'),
     ('4/12/2016 2:51', '1'),
     ('11/11/2015 22:09', '6'),
     ('5/26/2016 13:45', '2'),
     ('9/1/2016 16:27', '3'),
     ('5/3/2016 16:06', '9'),
     ('8/10/2016 5:58', '117'),
     ('10/22/2015 17:58', '1'),
     ('10/25/2015 21:53', '37'),
     ('1/25/2016 4:20', '5'),
     ('9/16/2016 11:46', '5'),
     ('8/18/2016 21:44', '16'),
     ('8/21/2016 7:46', '6'),
     ('5/6/2016 17:37', '26'),
     ('9/23/2016 20:23', '1'),
     ('1/3/2016 19:08', '17'),
     ('5/22/2016 10:33', '83'),
     ('4/11/2016 16:10', '9'),
     ('1/6/2016 0:50', '4'),
     ('3/7/2016 16:45', '6'),
     ('12/9/2015 17:35', '5'),
     ('5/18/2016 3:37', '8'),
     ('2/10/2016 14:51', '1'),
     ('3/19/2016 12:47', '11'),
     ('10/3/2015 19:46', '2'),
     ('9/27/2015 18:27', '1'),
     ('8/20/2016 10:08', '49'),
     ('2/2/2016 2:39', '1'),
     ('1/25/2016 3:23', '4'),
     ('3/23/2016 15:04', '3'),
     ('9/9/2015 15:08', '2'),
     ('12/28/2015 1:47', '37'),
     ('10/24/2015 17:58', '4'),
     ('11/28/2015 2:17', '6'),
     ('3/4/2016 15:45', '8'),
     ('9/11/2015 1:39', '65'),
     ('3/14/2016 12:12', '60'),
     ('1/16/2016 0:24', '2'),
     ('6/20/2016 10:25', '109'),
     ('1/3/2016 16:37', '12'),
     ('10/25/2015 22:17', '12'),
     ('9/20/2016 18:20', '3'),
     ('4/6/2016 8:47', '2'),
     ('3/22/2016 15:44', '3'),
     ('5/30/2016 19:57', '2'),
     ('11/16/2015 21:50', '1'),
     ('6/4/2016 14:50', '51'),
     ('1/19/2016 13:51', '2'),
     ('9/15/2016 8:40', '2'),
     ('5/2/2016 14:05', '2'),
     ('6/9/2016 18:54', '4'),
     ('3/25/2016 10:04', '90'),
     ('7/1/2016 19:01', '2'),
     ('3/4/2016 0:27', '1'),
     ('12/3/2015 15:35', '1'),
     ('3/18/2016 19:55', '81'),
     ('4/16/2016 14:19', '1'),
     ('8/7/2016 20:57', '514'),
     ('9/4/2016 14:17', '3'),
     ('6/12/2016 5:15', '16'),
     ('6/6/2016 6:21', '3'),
     ('3/22/2016 22:38', '3'),
     ('11/28/2015 3:03', '7'),
     ('1/30/2016 1:40', '21'),
     ('5/2/2016 10:52', '4'),
     ('7/14/2016 19:24', '3'),
     ('11/13/2015 11:04', '8'),
     ('5/19/2016 22:55', '5'),
     ('7/5/2016 18:51', '2'),
     ('4/1/2016 11:26', '7'),
     ('9/9/2015 15:16', '40'),
     ('9/16/2016 11:47', '1'),
     ('10/5/2015 19:36', '10'),
     ('1/27/2016 23:24', '1'),
     ('8/30/2016 14:48', '22'),
     ('1/15/2016 16:53', '4'),
     ('3/4/2016 21:13', '4'),
     ('1/10/2016 17:35', '15'),
     ('8/1/2016 4:44', '3'),
     ('2/24/2016 3:39', '3'),
     ('9/24/2016 16:03', '5'),
     ('8/29/2016 22:13', '5'),
     ('12/25/2015 10:31', '3'),
     ('10/9/2015 0:47', '1'),
     ('4/7/2016 20:12', '4'),
     ('2/14/2016 20:27', '6'),
     ('9/22/2015 4:41', '13'),
     ('7/24/2016 21:28', '1'),
     ('6/10/2016 1:01', '3'),
     ('10/27/2015 4:15', '62'),
     ('11/9/2015 19:35', '6'),
     ('8/19/2016 16:25', '3'),
     ('2/24/2016 5:16', '9'),
     ('1/18/2016 19:32', '43'),
     ('12/10/2015 16:22', '7'),
     ('11/9/2015 11:49', '1'),
     ('12/11/2015 21:56', '2'),
     ('8/30/2016 12:43', '6'),
     ('3/15/2016 0:41', '1'),
     ('10/16/2015 17:48', '23'),
     ('6/15/2016 9:04', '2'),
     ('9/16/2016 3:54', '2'),
     ('4/18/2016 22:01', '2'),
     ('1/27/2016 2:25', '2'),
     ('8/21/2016 8:42', '78'),
     ('4/30/2016 11:03', '4'),
     ('4/16/2016 8:47', '3'),
     ('2/10/2016 19:56', '4'),
     ('8/4/2016 8:13', '4'),
     ('4/8/2016 15:40', '128'),
     ('7/8/2016 21:06', '6'),
     ('4/28/2016 3:58', '32'),
     ('11/3/2015 19:24', '12'),
     ('9/6/2016 17:22', '7'),
     ('2/14/2016 21:23', '2'),
     ('5/5/2016 10:30', '3'),
     ('12/9/2015 11:09', '3'),
     ('5/8/2016 12:47', '3'),
     ('5/17/2016 0:59', '1'),
     ('8/30/2016 19:17', '4'),
     ('2/23/2016 18:44', '11'),
     ('8/15/2016 18:10', '7'),
     ('9/25/2015 5:58', '14'),
     ('3/17/2016 18:21', '4'),
     ('7/22/2016 17:13', '2'),
     ('4/21/2016 20:19', '7'),
     ('9/19/2016 12:54', '2'),
     ('11/23/2015 23:57', '18'),
     ('1/18/2016 20:20', '1'),
     ('12/10/2015 1:15', '4'),
     ('3/8/2016 13:45', '5'),
     ('9/20/2015 14:13', '1'),
     ('12/9/2015 14:56', '2'),
     ('7/12/2016 7:31', '1'),
     ('1/20/2016 19:09', '1'),
     ('8/25/2016 10:49', '2'),
     ('10/28/2015 13:38', '2'),
     ('8/10/2016 10:51', '2'),
     ('8/30/2016 11:08', '15'),
     ('7/29/2016 8:01', '9'),
     ('1/28/2016 21:27', '3'),
     ('8/25/2016 12:47', '2'),
     ('4/4/2016 19:12', '7'),
     ('5/7/2016 20:34', '2'),
     ('8/29/2016 19:33', '2'),
     ('4/22/2016 22:18', '4'),
     ('11/17/2015 6:42', '6'),
     ('6/6/2016 6:25', '8'),
     ('10/7/2015 23:21', '7'),
     ('5/29/2016 23:54', '65'),
     ('6/19/2016 6:03', '10'),
     ('4/7/2016 0:43', '2'),
     ('2/2/2016 0:20', '14'),
     ('2/15/2016 6:53', '50'),
     ('10/23/2015 21:32', '2'),
     ('1/12/2016 20:07', '4'),
     ('10/29/2015 16:50', '1'),
     ('8/14/2016 7:28', '12'),
     ('9/13/2016 2:52', '2'),
     ('1/7/2016 9:21', '4'),
     ('12/28/2015 20:21', '2'),
     ('7/16/2016 5:25', '3'),
     ('8/11/2016 19:09', '7'),
     ('1/2/2016 2:40', '1'),
     ('11/6/2015 15:59', '14'),
     ('1/29/2016 21:03', '520'),
     ('8/16/2016 18:04', '7'),
     ('8/28/2016 14:41', '3'),
     ('11/2/2015 10:59', '1'),
     ('1/14/2016 20:45', '40'),
     ('2/20/2016 2:58', '31'),
     ('7/1/2016 18:06', '9'),
     ('9/18/2016 15:51', '1'),
     ('3/7/2016 14:45', '94'),
     ('10/29/2015 4:51', '2'),
     ('6/3/2016 20:10', '3'),
     ('8/1/2016 15:01', '118'),
     ('11/13/2015 2:35', '2'),
     ('10/9/2015 19:47', '3'),
     ('1/31/2016 18:44', '7'),
     ('1/31/2016 13:52', '6'),
     ('12/17/2015 8:59', '1'),
     ('3/22/2016 2:42', '2'),
     ('10/21/2015 17:45', '3'),
     ('5/31/2016 21:53', '2'),
     ('1/12/2016 8:39', '12'),
     ('10/8/2015 10:15', '1'),
     ('4/1/2016 12:22', '5'),
     ('2/2/2016 22:15', '6'),
     ('4/1/2016 18:16', '3'),
     ('2/29/2016 21:55', '1'),
     ('11/9/2015 18:14', '27'),
     ('9/14/2016 14:31', '5'),
     ('3/9/2016 0:19', '1'),
     ('3/31/2016 17:37', '1'),
     ('5/21/2016 16:15', '8'),
     ('5/20/2016 21:17', '7'),
     ('5/12/2016 21:40', '2'),
     ('7/12/2016 8:30', '2'),
     ('6/29/2016 7:51', '53'),
     ('5/26/2016 15:11', '9'),
     ('9/12/2015 22:26', '7'),
     ('5/4/2016 1:16', '2'),
     ('9/23/2016 20:18', '477'),
     ('7/27/2016 19:16', '3'),
     ('7/8/2016 15:07', '111'),
     ('4/5/2016 4:55', '18'),
     ('12/8/2015 13:44', '4'),
     ('11/24/2015 4:59', '2'),
     ('10/27/2015 12:31', '17'),
     ('2/16/2016 3:25', '1'),
     ('8/3/2016 2:36', '6'),
     ('1/17/2016 21:49', '1'),
     ('6/23/2016 3:46', '31'),
     ('5/17/2016 14:07', '11'),
     ('2/17/2016 16:18', '1'),
     ('7/15/2016 23:35', '7'),
     ('6/3/2016 1:44', '1'),
     ('6/1/2016 14:48', '1'),
     ('4/17/2016 17:18', '96'),
     ('2/9/2016 10:56', '1'),
     ('11/30/2015 17:49', '5'),
     ('9/19/2016 16:04', '2'),
     ('10/20/2015 15:54', '1'),
     ('6/22/2016 12:23', '7'),
     ('6/24/2016 17:56', '6'),
     ('5/23/2016 5:11', '1'),
     ('8/22/2016 18:23', '3'),
     ('5/29/2016 22:38', '2'),
     ('1/2/2016 9:04', '1'),
     ('12/14/2015 7:57', '20'),
     ('8/29/2016 10:20', '4'),
     ('5/2/2016 0:38', '4'),
     ('3/21/2016 3:19', '15'),
     ('12/2/2015 3:53', '12'),
     ('7/29/2016 20:21', '4'),
     ('5/7/2016 4:37', '2'),
     ('5/28/2016 15:18', '6'),
     ('1/12/2016 7:16', '2'),
     ('3/18/2016 13:26', '1'),
     ('1/14/2016 18:12', '10'),
     ('3/19/2016 20:27', '5'),
     ('7/4/2016 13:09', '3'),
     ('9/11/2015 7:27', '2'),
     ('11/29/2015 11:23', '128'),
     ('8/24/2016 6:17', '1'),
     ('3/26/2016 6:24', '2'),
     ('12/7/2015 16:49', '1'),
     ('8/27/2016 18:05', '11'),
     ('11/19/2015 9:33', '4'),
     ('1/20/2016 11:19', '3'),
     ('9/8/2016 10:28', '10'),
     ('9/12/2016 7:47', '5'),
     ('9/19/2016 9:45', '7'),
     ('3/20/2016 2:37', '7'),
     ('1/13/2016 6:29', '14'),
     ('4/2/2016 21:56', '1'),
     ('3/22/2016 19:07', '6'),
     ('10/3/2015 21:01', '9'),
     ('8/14/2016 17:50', '135'),
     ('1/16/2016 23:23', '9'),
     ('9/14/2016 13:17', '7'),
     ('12/30/2015 10:41', '1'),
     ('9/22/2016 21:19', '5'),
     ('3/29/2016 13:00', '9'),
     ('7/12/2016 12:09', '9'),
     ('9/13/2016 21:43', '1'),
     ('4/4/2016 13:25', '14'),
     ('1/1/2016 15:29', '16'),
     ('9/11/2015 3:51', '2'),
     ('2/10/2016 15:12', '1'),
     ('12/30/2015 19:20', '37'),
     ('3/10/2016 3:35', '1'),
     ('12/22/2015 12:54', '1'),
     ('6/28/2016 10:14', '4'),
     ('6/11/2016 16:08', '1'),
     ('8/24/2016 12:07', '2'),
     ('6/15/2016 18:21', '1'),
     ('10/23/2015 2:22', '101'),
     ('12/3/2015 14:07', '2'),
     ('2/6/2016 19:39', '52'),
     ('12/23/2015 1:46', '2'),
     ('7/13/2016 19:14', '5'),
     ('5/28/2016 10:33', '12'),
     ('9/28/2015 7:05', '11'),
     ('7/26/2016 21:03', '5'),
     ('3/5/2016 9:56', '3'),
     ('8/18/2016 18:13', '3'),
     ('5/4/2016 8:10', '1'),
     ('2/3/2016 16:55', '4'),
     ('8/5/2016 20:59', '2'),
     ('2/27/2016 5:33', '7'),
     ('5/25/2016 19:59', '3'),
     ('5/26/2016 22:09', '13'),
     ('9/30/2015 3:39', '20'),
     ('11/9/2015 14:36', '3'),
     ('9/12/2016 8:33', '45'),
     ('9/17/2016 15:58', '1'),
     ('3/21/2016 19:21', '6'),
     ('6/28/2016 4:41', '4'),
     ('8/2/2016 20:27', '4'),
     ('4/25/2016 17:07', '3'),
     ('8/16/2016 14:59', '1'),
     ('11/7/2015 21:58', '17'),
     ('4/8/2016 21:13', '2'),
     ('12/2/2015 16:05', '3'),
     ('1/27/2016 0:18', '4'),
     ('6/23/2016 20:18', '2'),
     ('11/20/2015 0:25', '3'),
     ('4/27/2016 16:59', '10'),
     ('4/13/2016 16:06', '2'),
     ('8/18/2016 10:50', '50'),
     ('11/9/2015 17:31', '47'),
     ('6/17/2016 9:45', '1'),
     ('10/12/2015 7:17', '2'),
     ('9/13/2015 17:44', '5'),
     ('3/21/2016 5:00', '1'),
     ('11/9/2015 14:54', '28'),
     ('10/27/2015 18:37', '1'),
     ('7/26/2016 5:11', '3'),
     ('10/8/2015 11:31', '1'),
     ('11/3/2015 15:20', '22'),
     ('7/31/2016 21:09', '41'),
     ('8/15/2016 17:49', '1'),
     ('3/14/2016 0:51', '1'),
     ('12/28/2015 16:05', '1'),
     ('2/1/2016 23:10', '4'),
     ('9/23/2015 14:29', '1'),
     ('7/20/2016 0:25', '10'),
     ('4/6/2016 15:34', '5'),
     ('3/16/2016 22:40', '3'),
     ('7/11/2016 21:31', '147'),
     ('11/2/2015 15:53', '1'),
     ('9/9/2015 20:59', '1'),
     ('4/9/2016 14:52', '12'),
     ('5/3/2016 19:26', '5'),
     ('8/3/2016 17:27', '2'),
     ('9/7/2016 20:27', '3'),
     ('11/13/2015 11:09', '3'),
     ('7/5/2016 20:36', '2'),
     ('6/25/2016 0:22', '4'),
     ('5/11/2016 13:08', '3'),
     ('8/1/2016 12:45', '10'),
     ('2/12/2016 17:50', '5'),
     ('8/30/2016 15:27', '9'),
     ('10/1/2015 12:15', '2'),
     ('4/23/2016 11:43', '6'),
     ('8/29/2016 13:13', '6'),
     ('3/26/2016 0:22', '3'),
     ('8/12/2016 15:17', '3'),
     ('9/1/2016 4:31', '2'),
     ('8/2/2016 11:41', '2'),
     ('4/18/2016 7:03', '3'),
     ('1/25/2016 23:38', '8'),
     ('6/18/2016 15:15', '4'),
     ('11/4/2015 2:15', '4'),
     ('7/10/2016 22:15', '4'),
     ('5/11/2016 15:52', '144'),
     ('9/17/2016 23:48', '6'),
     ('6/15/2016 18:05', '11'),
     ('3/24/2016 23:24', '9'),
     ('11/25/2015 21:37', '1'),
     ('7/12/2016 19:24', '4'),
     ('9/23/2016 23:35', '6'),
     ('3/7/2016 11:37', '3'),
     ('1/13/2016 21:00', '131'),
     ('6/13/2016 15:53', '1'),
     ('2/3/2016 17:44', '1'),
     ('3/15/2016 2:36', '6'),
     ('1/21/2016 5:59', '4'),
     ('3/17/2016 16:53', '16'),
     ('4/26/2016 17:30', '4'),
     ('10/10/2015 8:28', '2'),
     ('4/12/2016 17:39', '3'),
     ('7/16/2016 20:03', '2'),
     ('4/25/2016 5:15', '2'),
     ('5/22/2016 10:59', '61'),
     ('12/24/2015 13:43', '31'),
     ('8/1/2016 0:23', '3'),
     ('1/26/2016 17:35', '2'),
     ('6/17/2016 13:56', '2'),
     ('11/6/2015 12:57', '5'),
     ('11/14/2015 19:28', '1'),
     ('3/24/2016 4:24', '11'),
     ('4/1/2016 17:07', '2'),
     ('9/12/2016 14:52', '3'),
     ('4/16/2016 6:51', '2'),
     ('9/18/2016 14:20', '1'),
     ('11/5/2015 4:53', '12'),
     ('8/3/2016 21:06', '3'),
     ('9/28/2015 2:42', '7'),
     ('3/2/2016 18:40', '1'),
     ('1/29/2016 3:06', '4'),
     ('1/8/2016 12:20', '1'),
     ('9/17/2015 14:45', '1'),
     ('8/30/2016 0:49', '3'),
     ('3/28/2016 1:28', '2'),
     ('6/30/2016 13:36', '6'),
     ('9/12/2015 5:50', '2'),
     ('3/9/2016 20:27', '3'),
     ('1/8/2016 21:27', '4'),
     ('4/5/2016 18:11', '1'),
     ('8/1/2016 21:03', '10'),
     ('2/4/2016 15:09', '8'),
     ('12/12/2015 7:15', '8'),
     ('9/15/2016 13:46', '1'),
     ('6/16/2016 20:25', '7'),
     ('5/31/2016 18:22', '34'),
     ('12/3/2015 11:50', '10'),
     ('8/14/2016 11:02', '1'),
     ('7/24/2016 12:28', '43'),
     ('11/21/2015 12:57', '9'),
     ('2/2/2016 12:25', '4'),
     ('6/29/2016 10:50', '1'),
     ('6/26/2016 14:16', '1'),
     ('4/15/2016 11:07', '3'),
     ('10/31/2015 2:06', '25'),
     ('4/2/2016 10:53', '1'),
     ('11/17/2015 21:00', '2'),
     ('11/28/2015 7:17', '9'),
     ('6/29/2016 14:43', '190'),
     ('2/15/2016 16:01', '11'),
     ('7/19/2016 20:35', '4'),
     ('11/10/2015 21:48', '9'),
     ('5/11/2016 14:36', '1'),
     ('1/17/2016 13:49', '1'),
     ('2/6/2016 4:38', '2'),
     ('6/12/2016 16:03', '6'),
     ('3/17/2016 22:41', '2'),
     ('6/13/2016 15:50', '1'),
     ('10/15/2015 19:16', '11'),
     ('8/4/2016 1:56', '3'),
     ('5/15/2016 3:01', '16'),
     ('1/31/2016 9:20', '1'),
     ('8/13/2016 2:19', '1'),
     ('5/3/2016 19:40', '1'),
     ('8/27/2016 17:07', '1'),
     ('2/13/2016 18:30', '7'),
     ('8/18/2016 6:05', '21'),
     ('7/19/2016 12:41', '2'),
     ('11/11/2015 16:28', '35'),
     ('1/20/2016 19:22', '4'),
     ('11/2/2015 11:51', '2'),
     ('2/12/2016 5:34', '16'),
     ('3/20/2016 7:52', '8'),
     ('7/14/2016 14:54', '1'),
     ('6/2/2016 13:44', '2'),
     ('4/22/2016 23:33', '5'),
     ('9/2/2016 2:08', '8'),
     ('9/29/2015 3:21', '33'),
     ('2/12/2016 2:50', '8'),
     ('6/16/2016 4:01', '10'),
     ('3/9/2016 22:44', '1'),
     ('10/28/2015 17:53', '2'),
     ('9/22/2015 21:55', '2'),
     ('10/9/2015 16:41', '5'),
     ('4/10/2016 16:12', '1'),
     ('1/8/2016 16:56', '5'),
     ('3/8/2016 18:35', '383'),
     ...]



With two new and empty dictionaries called `counts_by_hour` and `comments_by_hour`.

Loop through each row of `result_list`.

- Extract the **hour from the date**, which is the first element of the row.

- Use the **datetime.strptime()** method to parse the date and create a datetime object.

- Use the string we want to parse as the first argument and a string that specifies the format as the second argument.

- Use the **datetime.strftime()** method to select just the hour from the datetime object.


If the hour isn't a key in `counts_by_hour`:

- Create the key in `counts_by_hour`, and set it equal to 1.

- Create the key in `comments_by_hour`, and set it equal to the `comment number`.

If the hour is already a key in `counts_by_hour`:

- Increment the value in `counts_by_hour` by 1.

- Increment the value in `comments_by_hour` by the `comment number`.


```python
# ('8/28/2016 18:06', '2') <- date and number of comments (tuple)

counts_by_hour   = {}
comments_by_hour = {}

for row in result_list: # result_list content ('8/28/2016 18:06', '2')
    time_stamp = row[0]
    time_obj = dt.datetime.strptime(time_stamp, "%m/%d/%Y %H:%M")
    num_comments = int(row[1])
    
    hour = time_obj.hour
        
    if hour not in counts_by_hour:
        counts_by_hour[hour] = 1
        comments_by_hour[hour] = num_comments
    
    elif hour in counts_by_hour:
        counts_by_hour[hour] += 1
        comments_by_hour[hour] += num_comments
        
print(f"Counts by hour: {counts_by_hour}")
print("---")
print(f"Comments by hour: {comments_by_hour}")
```

    Counts by hour: {9: 45, 13: 85, 10: 59, 14: 107, 16: 108, 23: 68, 12: 73, 17: 100, 15: 116, 21: 109, 20: 80, 2: 58, 18: 109, 3: 54, 5: 46, 19: 110, 1: 60, 22: 71, 8: 48, 4: 47, 0: 55, 6: 44, 7: 34, 11: 58}
    ---
    Comments by hour: {9: 251, 13: 1253, 10: 793, 14: 1416, 16: 1814, 23: 543, 12: 687, 17: 1146, 15: 4477, 21: 1745, 20: 1722, 2: 1381, 18: 1439, 3: 421, 5: 464, 19: 1188, 1: 683, 22: 479, 8: 492, 4: 337, 0: 447, 6: 397, 7: 267, 11: 641}



`counts_by_hour`: contains the number of `ask posts` created during each hour of the day.

`comments_by_hour`: contains the corresponding number of `comments ask posts` created at each hour received.

## 6. Average number of comments on `Ask HN Posts` by Hour

Next, we'll use these two dictionaries `counts_by_hour` and `comments_by_hour` to calculate the average number of comments for posts created during each hour of the day.

Calculating the average number of comments per post, for posts created during each hour of the day.


```python
avg_by_hour = []

for horas in counts_by_hour: # {9: 45, 13: 85,...}
    avg_by_hour.append([horas, comments_by_hour[horas] / counts_by_hour[horas]])
    
avg_by_hour # result in another tupla
```




    [[9, 5.5777777777777775],
     [13, 14.741176470588234],
     [10, 13.440677966101696],
     [14, 13.233644859813085],
     [16, 16.796296296296298],
     [23, 7.985294117647059],
     [12, 9.41095890410959],
     [17, 11.46],
     [15, 38.5948275862069],
     [21, 16.009174311926607],
     [20, 21.525],
     [2, 23.810344827586206],
     [18, 13.20183486238532],
     [3, 7.796296296296297],
     [5, 10.08695652173913],
     [19, 10.8],
     [1, 11.383333333333333],
     [22, 6.746478873239437],
     [8, 10.25],
     [4, 7.170212765957447],
     [0, 8.127272727272727],
     [6, 9.022727272727273],
     [7, 7.852941176470588],
     [11, 11.051724137931034]]



**avg_by_hour:** Average number of comments for posts created during each hour of the day, and stored the results in a list of lists named.

## 7. Sorting and printing values from `avg_by_hour`

This format makes it difficult to identify the hours with the highest values:

        [[9, 5.5777777777777775],
         [13, 14.741176470588234],
         [10, 13.440677966101696],
         [14, 13.233644859813085],
         ...
         ]

Let's finish by sorting the list of lists `avg_by_hour` and printing the five highest values in a format that's easier to read.

Sorting the `avg_by_hour`.


```python
sorted_avg_by_hour = sorted(avg_by_hour, reverse=True)
sorted_avg_by_hour
```




    [[23, 7.985294117647059],
     [22, 6.746478873239437],
     [21, 16.009174311926607],
     [20, 21.525],
     [19, 10.8],
     [18, 13.20183486238532],
     [17, 11.46],
     [16, 16.796296296296298],
     [15, 38.5948275862069],
     [14, 13.233644859813085],
     [13, 14.741176470588234],
     [12, 9.41095890410959],
     [11, 11.051724137931034],
     [10, 13.440677966101696],
     [9, 5.5777777777777775],
     [8, 10.25],
     [7, 7.852941176470588],
     [6, 9.022727272727273],
     [5, 10.08695652173913],
     [4, 7.170212765957447],
     [3, 7.796296296296297],
     [2, 23.810344827586206],
     [1, 11.383333333333333],
     [0, 8.127272727272727]]



### Formating hours properly.


```python
date_format = "%H"

#15:00: 38.59 average comments per hour.

#print("Top 5 Hours for Ask Posts Comments:",'\n')

for row in sorted_avg_by_hour:
    horas = row[0]
    hora = str(horas)
    objeto_datetime =  dt.datetime.strptime(hora, date_format)
    hora = objeto_datetime.hour
    avg_comment = row[1]

    template = "At {h}:00 the average comments per post is {c:.2f}".format(h = horas, c = avg_comment )
    
    print(template)

```

    At 23:00 the average comments per post is 7.99
    At 22:00 the average comments per post is 6.75
    At 21:00 the average comments per post is 16.01
    At 20:00 the average comments per post is 21.52
    At 19:00 the average comments per post is 10.80
    At 18:00 the average comments per post is 13.20
    At 17:00 the average comments per post is 11.46
    At 16:00 the average comments per post is 16.80
    At 15:00 the average comments per post is 38.59
    At 14:00 the average comments per post is 13.23
    At 13:00 the average comments per post is 14.74
    At 12:00 the average comments per post is 9.41
    At 11:00 the average comments per post is 11.05
    At 10:00 the average comments per post is 13.44
    At 9:00 the average comments per post is 5.58
    At 8:00 the average comments per post is 10.25
    At 7:00 the average comments per post is 7.85
    At 6:00 the average comments per post is 9.02
    At 5:00 the average comments per post is 10.09
    At 4:00 the average comments per post is 7.17
    At 3:00 the average comments per post is 7.80
    At 2:00 the average comments per post is 23.81
    At 1:00 the average comments per post is 11.38
    At 0:00 the average comments per post is 8.13



```python
date_format = "%H" 

swap_avg_by_hour = []
#sorted_avg_by_hour
for row in sorted_avg_by_hour: #avg_by_hour
    puntos = row[1]
    b = str(row[0])
    objeto_datetime =  dt.datetime.strptime(b, date_format)
    hora = objeto_datetime.hour
    tupla = (puntos,hora)
    swap_avg_by_hour.append(tupla)

swap_avg_by_hour
```




    [(7.985294117647059, 23),
     (6.746478873239437, 22),
     (16.009174311926607, 21),
     (21.525, 20),
     (10.8, 19),
     (13.20183486238532, 18),
     (11.46, 17),
     (16.796296296296298, 16),
     (38.5948275862069, 15),
     (13.233644859813085, 14),
     (14.741176470588234, 13),
     (9.41095890410959, 12),
     (11.051724137931034, 11),
     (13.440677966101696, 10),
     (5.5777777777777775, 9),
     (10.25, 8),
     (7.852941176470588, 7),
     (9.022727272727273, 6),
     (10.08695652173913, 5),
     (7.170212765957447, 4),
     (7.796296296296297, 3),
     (23.810344827586206, 2),
     (11.383333333333333, 1),
     (8.127272727272727, 0)]




```python
sorted_swap = sorted(swap_avg_by_hour, reverse=True)
sorted_swap
```




    [(38.5948275862069, 15),
     (23.810344827586206, 2),
     (21.525, 20),
     (16.796296296296298, 16),
     (16.009174311926607, 21),
     (14.741176470588234, 13),
     (13.440677966101696, 10),
     (13.233644859813085, 14),
     (13.20183486238532, 18),
     (11.46, 17),
     (11.383333333333333, 1),
     (11.051724137931034, 11),
     (10.8, 19),
     (10.25, 8),
     (10.08695652173913, 5),
     (9.41095890410959, 12),
     (9.022727272727273, 6),
     (8.127272727272727, 0),
     (7.985294117647059, 23),
     (7.852941176470588, 7),
     (7.796296296296297, 3),
     (7.170212765957447, 4),
     (6.746478873239437, 22),
     (5.5777777777777775, 9)]




```python
date_format = "%H"

#15:00: 38.59 average comments per post.

for row in sorted_swap[0:4]:
    horas = row[1]
    avg_comment = row[0]
    
    plantilla = "At {h}:00 hours the average comments per post is {c:.2f}".format(h = horas, c = avg_comment )
    
    print(plantilla)
```

    At 15:00 hours the average comments per post is 38.59
    At 2:00 hours the average comments per post is 23.81
    At 20:00 hours the average comments per post is 21.52
    At 16:00 hours the average comments per post is 16.80


**Header looks like:**

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']

## A little step further away 

### Who are the most relevant authors in `ask_posts`on the forum?.

Within a community there is always a group of people who have written a larger number of posts. 

Let's see who are those who have written in `Ask post` as those who have done it in `Show post`


```python
def most_relevant_authors(kind_of_post):
    author = {}
    sort_dict = []
    
    for row in kind_of_post:
        who = row[5]
        
        if who in author:
            author[who] +=1
        else:
            author[who] = 1
            
    for (key,value) in author.items():
        sort_dict.append((value, key))
    
    return sorted(sort_dict, reverse = True)
```

### Most relevant authors in `Ask_posts` by number of times posted on the forum.


```python
most_relevant_authors(ask_posts)
```




    [(16, 'hoodoof'),
     (14, 'tmaly'),
     (9, 'whoishiring'),
     (7, 'prmph'),
     (7, 'hanniabu'),
     (6, 'tixocloud'),
     (5, 'vijayr'),
     (5, 'soulbadguy'),
     (5, 'sharemywin'),
     (5, 'rayalez'),
     (5, 'chirau'),
     (5, 'a_lifters_life'),
     (4, 'zuck9'),
     (4, 'wkoszek'),
     (4, 'selmat'),
     (4, 'probinso'),
     (4, 'nullundefined'),
     (4, 'neilsharma'),
     (4, 'max_'),
     (4, 'kevindeasis'),
     (4, 'holaboyperu'),
     (4, 'forgottenacc56'),
     (4, 'curiousgal'),
     (4, 'codegeek'),
     (4, 'cdvonstinkpot'),
     (4, 'basicscholar'),
     (4, 'baccheion'),
     (4, 'J-dawg'),
     (3, 'yeukhon'),
     (3, 'vinnyglennon'),
     (3, 'tuyguntn'),
     (3, 'tsaprailis'),
     (3, 'tonym9428'),
     (3, 'thrwawy20160421'),
     (3, 'thirstysusrando'),
     (3, 'techaddict009'),
     (3, 'simonebrunozzi'),
     (3, 'shubhamjain'),
     (3, 'shade23'),
     (3, 'sanosuke'),
     (3, 'philippnagel'),
     (3, 'networked'),
     (3, 'marktangotango'),
     (3, 'mangeletti'),
     (3, 'karimdag'),
     (3, 'jklein11'),
     (3, 'jason_slack'),
     (3, 'jacquesm'),
     (3, 'hellofunk'),
     (3, 'fratlas'),
     (3, 'ffggvv'),
     (3, 'enitihas'),
     (3, 'eecks'),
     (3, 'christopherDam'),
     (3, 'botw'),
     (3, 'bossx'),
     (3, 'augb'),
     (3, 'audace'),
     (3, 'aryamaan'),
     (3, 'andrewfromx'),
     (3, 'acidfreaks'),
     (3, 'abhishekdesai'),
     (3, '_spoonman'),
     (3, 'Raed667'),
     (3, 'LeicesterCity'),
     (2, 'zensavona'),
     (2, 'zenincognito'),
     (2, 'z0a'),
     (2, 'ywecur'),
     (2, 'wjh_'),
     (2, 'whack'),
     (2, 'warewolf'),
     (2, 'vonklaus'),
     (2, 'vital101'),
     (2, 'uptownhr'),
     (2, 'ugenetics'),
     (2, 'traviagio'),
     (2, 'trapped'),
     (2, 'the-dude'),
     (2, 'thakobyan'),
     (2, 'textread'),
     (2, 'tech_crawl_'),
     (2, 'teapot01'),
     (2, 'svirelka'),
     (2, 'supersan'),
     (2, 'specialist'),
     (2, 'spdustin'),
     (2, 'source99'),
     (2, 'sharmi'),
     (2, 'sdiq'),
     (2, 'sdegutis'),
     (2, 'scottndecker'),
     (2, 'schappim'),
     (2, 'samfisher83'),
     (2, 'sameernoorani'),
     (2, 'sabbasb'),
     (2, 'romellogoodman'),
     (2, 'priteshjain'),
     (2, 'poveritysucks'),
     (2, 'poushkar'),
     (2, 'ponderatul'),
     (2, 'pmoriarty'),
     (2, 'pixiez'),
     (2, 'personjerry'),
     (2, 'pedrodelfino'),
     (2, 'password03'),
     (2, 'palerdot'),
     (2, 'palakz'),
     (2, 'pal_25'),
     (2, 'notetoself'),
     (2, 'noobie'),
     (2, 'nonotmeplease'),
     (2, 'nicholas73'),
     (2, 'mxmpawn'),
     (2, 'mukgupta'),
     (2, 'montbonnot'),
     (2, 'minionslave'),
     (2, 'mikemajzoub'),
     (2, 'miguelrochefort'),
     (2, 'marmot777'),
     (2, 'maramono'),
     (2, 'm4nu'),
     (2, 'm1117'),
     (2, 'lscore720'),
     (2, 'lnalx'),
     (2, 'l33tbro'),
     (2, 'kyloren'),
     (2, 'krmmalik'),
     (2, 'kiloreux'),
     (2, 'jlebrech'),
     (2, 'jgotti92'),
     (2, 'isuckatcoding'),
     (2, 'idibidiart'),
     (2, 'hubatrix'),
     (2, 'hellomynameise'),
     (2, 'halotrope'),
     (2, 'hackerews'),
     (2, 'haack'),
     (2, 'going_to_800'),
     (2, 'gloves'),
     (2, 'giltleaf'),
     (2, 'giis'),
     (2, 'gavreh'),
     (2, 'foota'),
     (2, 'faizshah'),
     (2, 'exolymph'),
     (2, 'espitia'),
     (2, 'esob3'),
     (2, 'erkanerol'),
     (2, 'ejanus'),
     (2, 'dyeje'),
     (2, 'dutchbrit'),
     (2, 'dudeget'),
     (2, 'davidcoronado'),
     (2, 'daveloyall'),
     (2, 'coreyp_1'),
     (2, 'cmacole'),
     (2, 'chvid'),
     (2, 'cdnsteve'),
     (2, 'cauterized'),
     (2, 'canterburry'),
     (2, 'burritofanatic'),
     (2, 'brightball'),
     (2, 'bonobo3000'),
     (2, 'blabla_blublu'),
     (2, 'billconan'),
     (2, 'bflesch'),
     (2, 'avindroth'),
     (2, 'aprdm'),
     (2, 'anymys'),
     (2, 'alistproducer2'),
     (2, 'alexkehr'),
     (2, 'alexgpark'),
     (2, 'adzeds'),
     (2, 'adius'),
     (2, 'acconrad'),
     (2, 'abustamam'),
     (2, 'abba_fishhead'),
     (2, 'aaossa'),
     (2, 'Wonnk13'),
     (2, 'Vivavidaloca'),
     (2, 'Spooky23'),
     (2, 'SimplGy'),
     (2, 'Nemant'),
     (2, 'MuEta'),
     (2, 'Morgan17'),
     (2, 'LukeFitzpatrick'),
     (2, 'Kinnard'),
     (2, 'EleventhSun'),
     (2, 'DanPir'),
     (2, 'CoreSet'),
     (2, 'CarolineW'),
     (1, 'zorceta'),
     (1, 'zkirill'),
     (1, 'ziodave'),
     (1, 'zingplex'),
     (1, 'ziggystardust'),
     (1, 'zieseil'),
     (1, 'zeusdx'),
     (1, 'zerocrat'),
     (1, 'zehnfischer'),
     (1, 'zeeshanm'),
     (1, 'zebra'),
     (1, 'zatkin'),
     (1, 'zaroth'),
     (1, 'yyyuuu'),
     (1, 'yungGeez'),
     (1, 'yourabi'),
     (1, 'yoavanaki'),
     (1, 'yeowMeng'),
     (1, 'yellowboxtenant'),
     (1, 'ycjobquitter'),
     (1, 'ychandler'),
     (1, 'ybalkind'),
     (1, 'yarper'),
     (1, 'yahyaheee'),
     (1, 'yadongwen'),
     (1, 'y0ghur7_xxx'),
     (1, 'xzion'),
     (1, 'xwvvvvwx'),
     (1, 'xupybd'),
     (1, 'xhrpost'),
     (1, 'xdinomode'),
     (1, 'xchaotic'),
     (1, 'x0ry'),
     (1, 'x0054'),
     (1, 'wslh'),
     (1, 'wp_newb'),
     (1, 'wowzer'),
     (1, 'workshop_leads'),
     (1, 'woodstar'),
     (1, 'wofo'),
     (1, 'wnm'),
     (1, 'wlfsbrg'),
     (1, 'wirddin'),
     (1, 'winteriscoming'),
     (1, 'wingerlang'),
     (1, 'wilsonfiifi'),
     (1, 'williamle8300'),
     (1, 'willholloway'),
     (1, 'whyceethrowaway'),
     (1, 'whyasker'),
     (1, 'whistlerbrk'),
     (1, 'whicks'),
     (1, 'whattodo123'),
     (1, 'wfoweoi'),
     (1, 'westoque'),
     (1, 'westone'),
     (1, 'webbrahmin'),
     (1, 'wbjohn'),
     (1, 'wasif_hyder'),
     (1, 'warriorkitty'),
     (1, 'walrus01'),
     (1, 'vuyani'),
     (1, 'vulnersTeam'),
     (1, 'vtempest'),
     (1, 'vsergiu'),
     (1, 'vs2370'),
     (1, 'vram22'),
     (1, 'votr'),
     (1, 'voisin'),
     (1, 'vldx'),
     (1, 'vjoshi'),
     (1, 'vive-la-liberte'),
     (1, 'vitoralmeida'),
     (1, 'vishaldpatel'),
     (1, 'visawoes'),
     (1, 'visakanv'),
     (1, 'virgil_disgr4ce'),
     (1, 'vinaybn'),
     (1, 'vikasr111'),
     (1, 'victorhugo31337'),
     (1, 'victorantos'),
     (1, 'vfxGer'),
     (1, 'vdfs'),
     (1, 'vcald64'),
     (1, 'varadg'),
     (1, 'validuserfr'),
     (1, 'vain'),
     (1, 'vaib'),
     (1, 'uyoakaoma'),
     (1, 'username223'),
     (1, 'usermac'),
     (1, 'userAW'),
     (1, 'user7878'),
     (1, 'user321'),
     (1, 'usefulservices'),
     (1, 'usaphp'),
     (1, 'uptownfunk'),
     (1, 'uptown'),
     (1, 'unfortunateface'),
     (1, 'umitakcn'),
     (1, 'um304'),
     (1, 'uibkend'),
     (1, 'ucharmme'),
     (1, 'uberneo'),
     (1, 'tyurok'),
     (1, 'type0'),
     (1, 'twshoopboop'),
     (1, 'turd_ferguson'),
     (1, 'tsestrich'),
     (1, 'tsax'),
     (1, 'trying222'),
     (1, 'trtobe'),
     (1, 'traviswingo'),
     (1, 'trashpanda'),
     (1, 'trapperkeeper79'),
     (1, 'trahn'),
     (1, 'toss_it_away'),
     (1, 'tort_artificer'),
     (1, 'tootall'),
     (1, 'tonyle'),
     (1, 'tonydolore'),
     (1, 'tonteldoos'),
     (1, 'tomcam'),
     (1, 'tomas_interests'),
     (1, 'tom3k'),
     (1, 'todd8'),
     (1, 'tnorthcutt'),
     (1, 'tnitsche'),
     (1, 'tls-intercepted'),
     (1, 'tlong'),
     (1, 'tkfx'),
     (1, 'tkd'),
     (1, 'thunga'),
     (1, 'throwy666'),
     (1, 'throwwaway'),
     (1, 'throwthisawaypl'),
     (1, 'throwinitafter'),
     (1, 'throwawaynym'),
     (1, 'throwawaymaster'),
     (1, 'throwaway_rsu'),
     (1, 'throwaway_ldn'),
     (1, 'throwaway_askhn'),
     (1, 'throwaway_asker'),
     (1, 'throwaway_1122'),
     (1, 'throwaway8912'),
     (1, 'throwaway5023'),
     (1, 'throwaway2439'),
     (1, 'throwaway0727'),
     (1, 'throwaway007007'),
     (1, 'throw48596758'),
     (1, 'thr0waway1239'),
     (1, 'thorin'),
     (1, 'thomasfromcdnjs'),
     (1, 'thinkingserious'),
     (1, 'theviajerock'),
     (1, 'thetmkay'),
     (1, 'theoneone'),
     (1, 'thenomad'),
     (1, 'thegenius2000'),
     (1, 'thecuriousone'),
     (1, 'thecosas'),
     (1, 'the_wheel'),
     (1, 'the_cat_kittles'),
     (1, 'thanatropism'),
     (1, 'thalev'),
     (1, 'tevlon'),
     (1, 'testpass'),
     (1, 'tertius'),
     (1, 'ternbot'),
     (1, 'terda12'),
     (1, 'tekram'),
     (1, 'tehayj'),
     (1, 'teda'),
     (1, 'techlyf'),
     (1, 'techcorner'),
     (1, 'tcj_phx'),
     (1, 'tboyd47'),
     (1, 'tbirdz'),
     (1, 'taylormoon'),
     (1, 'tawaycreation'),
     (1, 'taurus'),
     (1, 'tarikozket'),
     (1, 'tanlermin'),
     (1, 'tango12'),
     (1, 'tananaev'),
     (1, 'tamersalama'),
     (1, 'tallerholler'),
     (1, 'takmusashi'),
     (1, 'tacticiankerala'),
     (1, 'tablock'),
     (1, 'tablet'),
     (1, 'syedkarim'),
     (1, 'swineflu'),
     (1, 'swimduck'),
     (1, 'sweetsweetpie'),
     (1, 'svepuri'),
     (1, 'svanderbleek'),
     (1, 'superplussed'),
     (1, 'superasn'),
     (1, 'sunasra'),
     (1, 'sudoherethere'),
     (1, 'stevofolife'),
     (1, 'stevewilhelm'),
     (1, 'stevesearer'),
     (1, 'steven_pack'),
     (1, 'stesch'),
     (1, 'stemuk'),
     (1, 'steejk'),
     (1, 'staticautomatic'),
     (1, 'startupsorter'),
     (1, 'startupdude69'),
     (1, 'stamps'),
     (1, 'ssaunier_'),
     (1, 'springogeek'),
     (1, 'springboard'),
     (1, 'spoonie'),
     (1, 'sph130'),
     (1, 'speeder'),
     (1, 'sparkling'),
     (1, 'spacewhale'),
     (1, 'soroso'),
     (1, 'soneca'),
     (1, 'somethingsimple'),
     (1, 'some_furry'),
     (1, 'soheil'),
     (1, 'soham'),
     (1, 'soared'),
     (1, 'snowse'),
     (1, 'snaga'),
     (1, 'smoyer'),
     (1, 'smockman36'),
     (1, 'smcguinness'),
     (1, 'smaili'),
     (1, 'sly010'),
     (1, 'slsii'),
     (1, 'slordy'),
     (1, 'slice-beans'),
     (1, 'slagfart'),
     (1, 'skewart'),
     (1, 'sk14'),
     (1, 'sixQuarks'),
     (1, 'siquick'),
     (1, 'sinnska'),
     (1, 'singularitynear'),
     (1, 'singold'),
     (1, 'simon_acca'),
     (1, 'simbalion'),
     (1, 'sigmaml'),
     (1, 'sigdante'),
     (1, 'siavosh'),
     (1, 'shirman'),
     (1, 'sharjeel'),
     (1, 'shanwang'),
     (1, 'shadowycoder'),
     (1, 'sha1-1b141e'),
     (1, 'sgustard'),
     (1, 'servo'),
     (1, 'servlate'),
     (1, 'sergiotapia'),
     (1, 'semicolondev'),
     (1, 'selbyk'),
     (1, 'sedzia'),
     (1, 'sec_throwaway'),
     (1, 'sebst'),
     (1, 'sebg'),
     (1, 'sebastianconcpt'),
     (1, 'scaredtolaunch'),
     (1, 'scandox'),
     (1, 'sashazykov'),
     (1, 'sarreph'),
     (1, 'santoshmaharshi'),
     (1, 'sanmon3186'),
     (1, 'sanjeetsuhag'),
     (1, 'sandis'),
     (1, 'samstave'),
     (1, 'samsolomon'),
     (1, 'sama'),
     (1, 'sago'),
     (1, 'sagarghai'),
     (1, 's-stude'),
     (1, 'ryanlm'),
     (1, 'rvpolyak'),
     (1, 'rustymirror'),
     (1, 'runlivemem'),
     (1, 'rtfpessoa'),
     (1, 'rsmoore215'),
     (1, 'rquantz'),
     (1, 'roykolak'),
     (1, 'roschdal'),
     (1, 'rokhayakebe'),
     (1, 'rohit6223'),
     (1, 'rodolphoarruda'),
     (1, 'rochak'),
     (1, 'robmiller'),
     (1, 'robbystout'),
     (1, 'rmkahler'),
     (1, 'rm_-rf_slash'),
     (1, 'rm2904'),
     (1, 'rloc'),
     (1, 'rkhraishi'),
     (1, 'rk0567'),
     (1, 'ritchiea'),
     (1, 'ripitrust'),
     (1, 'riotvan'),
     (1, 'riebschlager'),
     (1, 'rickyrecon'),
     (1, 'rickdale'),
     (1, 'richerlariviere'),
     (1, 'ricardobeat'),
     (1, 'rgovind'),
     (1, 'rgdzz3'),
     (1, 'reg29'),
     (1, 'refrigerator'),
     (1, 'reedlaw'),
     (1, 'redxblood'),
     (1, 'red_fox'),
     (1, 'recmend'),
     (1, 'realmunk'),
     (1, 'readchallenged'),
     (1, 'reacharavindh'),
     (1, 'rbcgerard'),
     (1, 'raymondgh'),
     (1, 'raykanani99'),
     (1, 'rayascott'),
     (1, 'ratsimihah'),
     (1, 'rangerunseen'),
     (1, 'randy_gilette'),
     (1, 'ranaway'),
     (1, 'ramblerman'),
     (1, 'rajeshmr'),
     (1, 'raiseta'),
     (1, 'raimille1'),
     (1, 'rahimnathwani'),
     (1, 'raddad'),
     (1, 'racistcompanies'),
     (1, 'qwrshhjkkl'),
     (1, 'qwer'),
     (1, 'quii'),
     (1, 'questionsforhn'),
     (1, 'questionr'),
     (1, 'queeerkopf'),
     (1, 'quantifiedCoder'),
     (1, 'pzk1'),
     (1, 'pygy_'),
     (1, 'pvinis'),
     (1, 'push7joshi'),
     (1, 'purplerabbit'),
     (1, 'ptype'),
     (1, 'psmutha'),
     (1, 'pravint'),
     (1, 'praveenh'),
     (1, 'prattbhatt'),
     (1, 'prats226'),
     (1, 'prateekbhatt'),
     (1, 'pouzy'),
     (1, 'poppup'),
     (1, 'popekpampam'),
     (1, 'poops'),
     (1, 'ponderingHplus'),
     (1, 'polym'),
     (1, 'polskibus'),
     (1, 'pollilop'),
     (1, 'plugnburn'),
     (1, 'plet'),
     (1, 'pjungwir'),
     (1, 'pizu'),
     (1, 'pipu'),
     (1, 'pinkunicorn'),
     (1, 'pigpigs'),
     (1, 'phystoind'),
     (1, 'philip1209'),
     (1, 'phankinson'),
     (1, 'peterchane'),
     (1, 'personlurking'),
     (1, 'perakojotgenije'),
     (1, 'penguinlinux'),
     (1, 'pedrorijo91'),
     (1, 'pbhowmic'),
     (1, 'pattle'),
     (1, 'passive'),
     (1, 'parnor'),
     (1, 'panicocats'),
     (1, 'pandatigox'),
     (1, 'palidanx'),
     (1, 'paglia_s'),
     (1, 'paekut'),
     (1, 'oxplot'),
     (1, 'osazuwa'),
     (1, 'orless'),
     (1, 'orangeplus'),
     (1, 'orangepenguin'),
     (1, 'orange_county'),
     (1, 'optionadvice'),
     (1, 'oolongCat'),
     (1, 'onecooldev24'),
     (1, 'omilu'),
     (1, 'oliverjudge'),
     (1, 'oliv__'),
     (1, 'old_young_guy'),
     (1, 'olalonde'),
     (1, 'ojbrien'),
     (1, 'ohgh1ieD'),
     (1, 'o_safadinho'),
     (1, 'o_s_m'),
     (1, 'nyc111'),
     (1, 'nxzero'),
     (1, 'nstart'),
     (1, 'npguy'),
     (1, 'nowherenice'),
     (1, 'notinreallife'),
     (1, 'nodivbyzero'),
     (1, 'noaclpo'),
     (1, 'nns'),
     (1, 'nnd'),
     (1, 'nkobeissi'),
     (1, 'ninja_to_be'),
     (1, 'ninadmhatre'),
     (1, 'nikon'),
     (1, 'nikobellic'),
     (1, 'nikitarajdan'),
     (1, 'nikhildaga'),
     (1, 'nickfranky'),
     (1, 'nichochar'),
     (1, 'newtabpage'),
     (1, 'newman8r'),
     (1, 'newbie_hacker'),
     (1, 'new_challenger'),
     (1, 'netzwerk'),
     (1, 'netshade'),
     (1, 'nethsix'),
     (1, 'netgusto'),
     (1, 'netcraft'),
     (1, 'nerva'),
     (1, 'neogenix'),
     (1, 'nenadg'),
     (1, 'neltnerb'),
     (1, 'neilmack'),
     (1, 'neilellis'),
     (1, 'neelkadia'),
     (1, 'needjshelp'),
     (1, 'ne01'),
     (1, 'ncodes'),
     (1, 'ncarlson'),
     (1, 'navd'),
     (1, 'nathan_f77'),
     (1, 'nate_robo'),
     (1, 'nanospeck'),
     (1, 'namenotgiven'),
     (1, 'nambante'),
     (1, 'n72'),
     (1, 'mvdwoord'),
     (1, 'muzster'),
     (1, 'musicaldope'),
     (1, 'musha68k'),
     (1, 'mus1cfl0w'),
     (1, 'muratk'),
     (1, 'muddyrivers'),
     (1, 'msurekci'),
     (1, 'mstipetic'),
     (1, 'mskierkowski'),
     (1, 'msh'),
     (1, 'mrwnmonm'),
     (1, 'mrtsepelev'),
     (1, 'mrnoname'),
     (1, 'mrborgen'),
     (1, 'mproud'),
     (1, 'movedx'),
     (1, 'mortal'),
     (1, 'morinted'),
     (1, 'mooreds'),
     (1, 'moka_crazy15'),
     (1, 'mod50ack'),
     (1, 'mmanfrin'),
     (1, 'mkra'),
     (1, 'mkagenius'),
     (1, 'mixmax'),
     (1, 'mitm2mitm'),
     (1, 'mirchada776'),
     (1, 'mirceasoaica'),
     (1, 'mirap'),
     (1, 'mintplant'),
     (1, 'mingabunga'),
     (1, 'millzlane'),
     (1, 'mikeyanderson'),
     (1, 'mikeschmatz'),
     (1, 'mijustin'),
     (1, 'migthor'),
     (1, 'midhem'),
     (1, 'microsby0'),
     (1, 'microman'),
     (1, 'michalu'),
     (1, 'michaeldunworth'),
     (1, 'mgamache'),
     (1, 'mgalka'),
     (1, 'mey'),
     (1, 'meturtle'),
     (1, 'meticulouschris'),
     (1, 'meta_pseudo'),
     (1, 'merqurio'),
     (1, 'melle'),
     (1, 'mellamoyo'),
     (1, 'meeper16'),
     (1, 'medhir'),
     (1, 'mdevere'),
     (1, 'mcthrowaway'),
     (1, 'mchahn'),
     (1, 'mc_vala'),
     (1, 'maxxxxx'),
     (1, 'maxsavin'),
     (1, 'maxro'),
     (1, 'mattedigital'),
     (1, 'mattchue'),
     (1, 'matheweis'),
     (1, 'master_yoda_1'),
     (1, 'master_plan'),
     (1, 'marvel_boy'),
     (1, 'martinshen'),
     (1, 'martinald'),
     (1, 'markyc'),
     (1, 'markwaldron'),
     (1, 'markhall'),
     (1, 'mark_l_watson'),
     (1, 'mariocesar'),
     (1, 'marginalcodex'),
     (1, 'marcamillion'),
     (1, 'maratd'),
     (1, 'mapmeld'),
     (1, 'mapcars'),
     (1, 'maouida'),
     (1, 'manibatra'),
     (1, 'magic_man'),
     (1, 'macarthy12'),
     (1, 'm0dE'),
     (1, 'luksi'),
     (1, 'lukeHeuer'),
     (1, 'lukasm'),
     (1, 'luck87'),
     (1, 'luchadorvader'),
     (1, 'lucasch'),
     (1, 'lsiebert'),
     (1, 'lsc'),
     (1, 'ls66'),
     (1, 'losingcontrol'),
     (1, 'lorenzhs'),
     (1, 'lonely_rebel'),
     (1, 'lollinghard'),
     (1, 'lokio9'),
     (1, 'logn'),
     (1, 'logicb'),
     (1, 'lobster_johnson'),
     (1, 'lngtmconsultant'),
     (1, 'ljim4a'),
     (1, 'lj3'),
     (1, 'livus'),
     (1, 'lindx'),
     (1, 'lilcarlyung'),
     (1, 'lighttower'),
     (1, 'lifeisstillgood'),
     (1, 'lesvizit'),
     (1, 'leksak'),
     (1, 'leavemealone'),
     (1, 'leandot'),
     (1, 'ldom22'),
     (1, 'lazyjones'),
     (1, 'laxk'),
     (1, 'laurencei'),
     (1, 'lagbaja'),
     (1, 'l3robot'),
     (1, 'l1n'),
     (1, 'kuro-kuris'),
     (1, 'kuon'),
     (1, 'ksashikumar'),
     (1, 'kosker'),
     (1, 'kolanos'),
     (1, 'kokonotu'),
     (1, 'kmfrk'),
     (1, 'kluck'),
     (1, 'kleer001'),
     (1, 'klaut'),
     (1, 'kkt262'),
     (1, 'kishansundar'),
     (1, 'kirykl'),
     (1, 'kirk21'),
     (1, 'kiraken'),
     (1, 'kierantie'),
     (1, 'kiddz'),
     (1, 'khyur'),
     (1, 'kfalion'),
     (1, 'keywonc'),
     (1, 'keyle'),
     (1, 'kexari'),
     (1, 'kevinsimper'),
     (1, 'kevinSuttle'),
     (1, 'kevando'),
     (1, 'kelvincobanaj'),
     (1, 'kcoleman731'),
     (1, 'kbouw'),
     (1, 'kayman'),
     (1, 'katpas'),
     (1, 'kartikkumar'),
     (1, 'karmacondon'),
     (1, 'karamazov'),
     (1, 'kaptain'),
     (1, 'kanso'),
     (1, 'kaishiro'),
     (1, 'jzelinskie'),
     (1, 'jxm262'),
     (1, 'jwdunne'),
     (1, 'justplay'),
     (1, 'julie1'),
     (1, 'juandazapata'),
     (1, 'jsnathan'),
     (1, 'jph'),
     (1, 'jozydapozy'),
     (1, 'journeyadv'),
     (1, 'joshstrange'),
     (1, 'josephjrobison'),
     (1, 'josep2'),
     (1, 'jorgecurio'),
     (1, 'jordanchan'),
     (1, 'jonjlee'),
     (1, 'jondubois'),
     (1, 'jonahx'),
     (1, 'jollychang'),
     (1, 'johnvic'),
     (1, 'johnnydoebk'),
     (1, 'johnnycarcin'),
     (1, 'johnhess'),
     (1, 'johan_larson'),
     (1, 'joenathan'),
     (1, 'joelx'),
     (1, 'jodrellblank'),
     (1, 'jobseekerThrowA'),
     (1, 'jnardiello'),
     (1, 'jmzbond'),
     (1, 'jmakaa'),
     (1, 'jmaccabee'),
     (1, 'jlos'),
     (1, 'jkuria'),
     (1, 'jjoe'),
     (1, 'jjensen90'),
     (1, 'jimmygatz'),
     (1, 'jharohit'),
     (1, 'jhamar'),
     (1, 'jghn'),
     (1, 'jfornear'),
     (1, 'jeshan25'),
     (1, 'jdmoreira'),
     (1, 'jdironman'),
     (1, 'jcuga'),
     (1, 'jc_811'),
     (1, 'jbverschoor'),
     (1, 'jbooj'),
     (1, 'jbnicolai'),
     (1, 'japhyr'),
     (1, 'jampa-uchoa'),
     (1, 'jamesxx'),
     (1, 'jameshk'),
     (1, 'jamesdharper3'),
     (1, 'jakejake'),
     (1, 'jaisingh'),
     (1, 'jacke'),
     (1, 'jabo'),
     (1, 'j2bax'),
     (1, 'iyogeshjoshi'),
     (1, 'ivineet'),
     (1, 'itsashis4u'),
     (1, 'itchynosedev'),
     (1, 'isoos'),
     (1, 'ishener'),
     (1, 'irl_zebra'),
     (1, 'iqonik'),
     (1, 'ipselon'),
     (1, 'iperez'),
     (1, 'internal_tools'),
     (1, 'insamniac'),
     (1, 'inputcoffee'),
     (1, 'inlineint'),
     (1, 'ingmarheinrich'),
     (1, 'infocollector'),
     (1, 'inefficientm'),
     (1, 'imrehg'),
     (1, 'impostervt'),
     (1, 'impish19'),
     (1, 'imjk'),
     (1, 'imjacobclark'),
     (1, 'imakesnowflakes'),
     (1, 'ilolu'),
     (1, 'ichinisanshi'),
     (1, 'ianpurton'),
     (1, 'ianderf'),
     (1, 'iamsohungry'),
     (1, 'iDemonix'),
     (1, 'i336_'),
     (1, 'hysan'),
     (1, 'hyperpallium'),
     (1, 'hugomano'),
     (1, 'huevosabio'),
     (1, 'hotpockets'),
     (1, 'horsecaptin'),
     (1, 'hooliganpete'),
     (1, 'hnthrowaway488'),
     (1, 'hndatapagan'),
     (1, 'hkyeti'),
     (1, 'hippychap'),
     (1, 'himanshuy'),
     (1, 'hillstation21'),
     (1, 'hexman'),
     (1, 'heuermh'),
     (1, 'henryzhang0304'),
     (1, 'hemapani'),
     (1, 'helplessdev'),
     (1, 'heike'),
     (1, 'hehenotnow'),
     (1, 'hbhakhra'),
     (1, 'hbbio'),
     (1, 'have_faith'),
     (1, 'hartator'),
     (1, 'harshitj'),
     (1, 'hargup'),
     (1, 'harel'),
     (1, 'hanyoon'),
     (1, 'haidrali'),
     (1, 'hadenough'),
     (1, 'hactually'),
     (1, 'hackathonguy'),
     (1, 'gunnark01'),
     (1, 'gull'),
     (1, 'guillegette'),
     (1, 'guillaumec'),
     (1, 'gtirloni'),
     (1, 'gsklee'),
     (1, 'gs7'),
     (1, 'greenspot'),
     (1, 'grantham'),
     (1, 'grammernerd'),
     (1, 'graham1776'),
     (1, 'grafelic'),
     (1, 'gradinaru_alex'),
     (1, 'gpestana'),
     (1, 'goshx'),
     (1, 'gorer'),
     (1, 'goostavos'),
     (1, 'googsomeday'),
     (1, 'goodfellaw'),
     (1, 'goodcharacters'),
     (1, 'godot'),
     (1, 'glynjackson'),
     (1, 'glossyscr'),
     (1, 'glondi'),
     (1, 'glitch003'),
     (1, 'glasnoster'),
     (1, 'glaberficken'),
     (1, 'gizzlon'),
     (1, 'gisnotgoogle'),
     (1, 'gioscarab'),
     (1, 'ginsurge'),
     (1, 'giftoolbox'),
     (1, 'ghrifter'),
     (1, 'ghoshbishakh'),
     (1, 'ghettosoak'),
     (1, 'ggregoire'),
     (1, 'ggonweb'),
     (1, 'gghyslain'),
     (1, 'gearoidoc'),
     (1, 'gdaz'),
     (1, 'garkimasera'),
     (1, 'gammabeta'),
     (1, 'gamekathu'),
     (1, 'galfarragem'),
     (1, 'galazzah'),
     (1, 'galaktor'),
     (1, 'g123g'),
     (1, 'fyrejuggler'),
     (1, 'funkyy'),
     (1, 'frr149'),
     (1, 'franze'),
     (1, 'frankacter'),
     (1, 'franco456'),
     (1, 'franciscop'),
     (1, 'frame_perfect'),
     (1, 'frade33'),
     (1, 'foxhedgehog'),
     (1, 'forkandwait'),
     (1, 'forgotAgain'),
     (1, 'forcer'),
     (1, 'florincm'),
     (1, 'flippyhead'),
     (1, 'flashburn'),
     (1, 'flarion'),
     (1, 'fladd'),
     (1, 'fizzbucket'),
     (1, 'firingpii'),
     (1, 'filleokus'),
     (1, 'fha'),
     (1, 'fenier'),
     (1, 'fairpx'),
     (1, 'fabulist'),
     (1, 'fabianlindfors'),
     (1, 'ezl'),
     (1, 'evolve2k'),
     (1, 'evm9'),
     (1, 'every_other'),
     (1, 'event_noob'),
     (1, 'essofluffy'),
     (1, 'escapologybb'),
     (1, 'erik14th'),
     (1, 'ericax'),
     ...]



### Most relevant authors in `Show_posts`


```python
most_relevant_authors(show_posts)
```




    [(4, 'vipul4vb'),
     (4, 'soheil'),
     (4, 'max0563'),
     (4, 'iisbum'),
     (4, 'emeth'),
     (4, 'chinchang'),
     (4, 'alexellisuk'),
     (3, 'stockkid'),
     (3, 'mojoe'),
     (3, 'gk_brown'),
     (3, 'fiatjaf'),
     (3, 'awwstn'),
     (2, 'zaytoun'),
     (2, 'vishaldpatel'),
     (2, 'viebel'),
     (2, 'traviswingo'),
     (2, 'tinjam'),
     (2, 'tgoldberg'),
     (2, 'syrusakbary'),
     (2, 'sunnynagra'),
     (2, 'stagename'),
     (2, 'solusipse'),
     (2, 'snehesht'),
     (2, 'shaharsol'),
     (2, 'ruiramos'),
     (2, 'rooviz'),
     (2, 'robot'),
     (2, 'rezashirazian'),
     (2, 'rayalez'),
     (2, 'qrv3w'),
     (2, 'powturbo'),
     (2, 'pezza3434'),
     (2, 'natsu90'),
     (2, 'mrzool'),
     (2, 'morninj'),
     (2, 'maxpert'),
     (2, 'marco1'),
     (2, 'm52go'),
     (2, 'licobo'),
     (2, 'laarc'),
     (2, 'ksowocki'),
     (2, 'knutmartin'),
     (2, 'keithwhor'),
     (2, 'jointhebox'),
     (2, 'jjets718'),
     (2, 'jcuga'),
     (2, 'impostervt'),
     (2, 'imakesnowflakes'),
     (2, 'harrisreynolds'),
     (2, 'gioscarab'),
     (2, 'fredrivett'),
     (2, 'fibo'),
     (2, 'fatiherikli'),
     (2, 'eon01'),
     (2, 'ejcx'),
     (2, 'dkoplovich'),
     (2, 'djyde'),
     (2, 'danielrw7'),
     (2, 'dallamaneni'),
     (2, 'daiwei'),
     (2, 'cillian'),
     (2, 'cbabraham'),
     (2, 'bucaran'),
     (2, 'browseu'),
     (2, 'breeve'),
     (2, 'brakmic'),
     (2, 'bbrez1'),
     (2, 'bbrennan'),
     (2, 'aub3bhat'),
     (2, 'asadjb'),
     (2, 'architv07'),
     (2, 'alexed'),
     (2, 'alehander42'),
     (2, 'adibalcan'),
     (2, 'Trof'),
     (2, 'TimLeland'),
     (2, 'StavrosK'),
     (2, 'NicoJuicy'),
     (2, 'EllipticCurve'),
     (2, 'AlexKaul'),
     (1, 'zzzhan'),
     (1, 'zwischenzug'),
     (1, 'zunzun'),
     (1, 'zserge'),
     (1, 'zoran119'),
     (1, 'zoozla'),
     (1, 'zongitsrinzler'),
     (1, 'zizh'),
     (1, 'zipBoard'),
     (1, 'zielmicha'),
     (1, 'zhegwood'),
     (1, 'zackify'),
     (1, 'zachklipp'),
     (1, 'yrochat'),
     (1, 'yourlocalcousin'),
     (1, 'yoshuaw'),
     (1, 'yoshiokatsuneo'),
     (1, 'yongelee'),
     (1, 'ykadosh'),
     (1, 'yefim'),
     (1, 'ybrs'),
     (1, 'yati'),
     (1, 'yask123'),
     (1, 'yaronn01'),
     (1, 'yangmillstheory'),
     (1, 'yalimgerger'),
     (1, 'xwowsersx'),
     (1, 'xojoc'),
     (1, 'xenolf'),
     (1, 'xcatliu'),
     (1, 'wwwtyro'),
     (1, 'wtracy'),
     (1, 'wsieroci'),
     (1, 'wsdookadr'),
     (1, 'wscott'),
     (1, 'wolfico'),
     (1, 'wiso'),
     (1, 'winstonyw'),
     (1, 'willlma'),
     (1, 'willkim'),
     (1, 'will_brown'),
     (1, 'wiemee'),
     (1, 'whockey'),
     (1, 'whitef0x'),
     (1, 'whiplashoo'),
     (1, 'wf902'),
     (1, 'wesbos'),
     (1, 'wener'),
     (1, 'welder'),
     (1, 'welanes'),
     (1, 'wdstash'),
     (1, 'wayofthesamurai'),
     (1, 'wassago'),
     (1, 'waslu'),
     (1, 'wannatouchmyfro'),
     (1, 'w0ts0n'),
     (1, 'vvviperrr'),
     (1, 'vsiden'),
     (1, 'vsergiu'),
     (1, 'vpanyam'),
     (1, 'volker48'),
     (1, 'vkorsunov'),
     (1, 'vjeux'),
     (1, 'viking2917'),
     (1, 'victorgama'),
     (1, 'vfaronov'),
     (1, 'vesln'),
     (1, 'verdantlabs'),
     (1, 'veniversum'),
     (1, 'veddox'),
     (1, 'vdnkh'),
     (1, 'valera_rozuvan'),
     (1, 'vadimbaryshev'),
     (1, 'vackosar'),
     (1, 'ukz'),
     (1, 'tzier'),
     (1, 'tunavargi'),
     (1, 'tscizzle'),
     (1, 'travocado'),
     (1, 'train255'),
     (1, 'tonyspiro'),
     (1, 'tonyonodi'),
     (1, 'tommyallen'),
     (1, 'tomitm'),
     (1, 'tomgenoni'),
     (1, 'toast76'),
     (1, 'tindrlabs'),
     (1, 'timvdalen'),
     (1, 'timqian'),
     (1, 'timhj'),
     (1, 'timedivers'),
     (1, 'tim_nuwin'),
     (1, 'tim333'),
     (1, 'tidwall'),
     (1, 'thoughtpalette'),
     (1, 'thomasrep'),
     (1, 'thomas101'),
     (1, 'thisisgustav'),
     (1, 'thewhitetulip'),
     (1, 'thesubroot'),
     (1, 'theodorewiles'),
     (1, 'theocs'),
     (1, 'thenomad'),
     (1, 'theharsh'),
     (1, 'the_wheel'),
     (1, 'th-ai'),
     (1, 'terjeto'),
     (1, 'teer'),
     (1, 'tcit'),
     (1, 'tc1222'),
     (1, 'tazer'),
     (1, 'tacone'),
     (1, 't3nary'),
     (1, 'synapticrelease'),
     (1, 'swf'),
     (1, 'svlasov'),
     (1, 'suyash93'),
     (1, 'susam'),
     (1, 'superasn'),
     (1, 'sumitz'),
     (1, 'submitted'),
     (1, 'submitstartup'),
     (1, 'subliminalzen'),
     (1, 'stugrey'),
     (1, 'stucat'),
     (1, 'stroz'),
     (1, 'stressfree'),
     (1, 'stindle'),
     (1, 'steve_wilson'),
     (1, 'steve-benjamins'),
     (1, 'stephensonsco'),
     (1, 'stephenoman'),
     (1, 'stenius'),
     (1, 'startlaunch'),
     (1, 'starlineventure'),
     (1, 'ss180'),
     (1, 'srikieonline'),
     (1, 'spenceryang'),
     (1, 'sp0rkyd0rky'),
     (1, 'soulchild37'),
     (1, 'soral'),
     (1, 'sonaal'),
     (1, 'some1else'),
     (1, 'solson'),
     (1, 'softvar'),
     (1, 'snitzr'),
     (1, 'smithgeek'),
     (1, 'sleepstosanta'),
     (1, 'slaask'),
     (1, 'skiano'),
     (1, 'skerit'),
     (1, 'sitepassword'),
     (1, 'siquick'),
     (1, 'simonnorberg'),
     (1, 'simonk'),
     (1, 'simonebrunozzi'),
     (1, 'simon_vetter'),
     (1, 'sidi'),
     (1, 'siddharthkp'),
     (1, 'sidcool'),
     (1, 'shutton'),
     (1, 'shuheng'),
     (1, 'shubhamjain'),
     (1, 'shnere'),
     (1, 'shinao'),
     (1, 'sheerun'),
     (1, 'sharkdp'),
     (1, 'shanwang'),
     (1, 'shagunsodhani'),
     (1, 'seyz'),
     (1, 'sevzi7'),
     (1, 'serrisande'),
     (1, 'senoroink'),
     (1, 'seletskiy'),
     (1, 'sebastiank123'),
     (1, 'seasonedschemer'),
     (1, 'seanpk8'),
     (1, 'seanosaur'),
     (1, 'sdogruyol'),
     (1, 'scrollaway'),
     (1, 'scprodigy'),
     (1, 'scott_b'),
     (1, 'saola-app'),
     (1, 'sandheepgr'),
     (1, 'samil'),
     (1, 'samdfonseca'),
     (1, 'salparadi'),
     (1, 'salomelunarojas'),
     (1, 'sahadeva'),
     (1, 'saghm'),
     (1, 'sachinchoolur'),
     (1, 'sachinag'),
     (1, 'ryangilbert'),
     (1, 'rwilinski'),
     (1, 'rvivek'),
     (1, 'ruyadorno'),
     (1, 'rukshn'),
     (1, 'rsbadger'),
     (1, 'rrdelaney'),
     (1, 'rowanmanning'),
     (1, 'rory096'),
     (1, 'rombri'),
     (1, 'romaincointepas'),
     (1, 'rohak'),
     (1, 'rodstod'),
     (1, 'rodrigoalima99'),
     (1, 'rocksoug'),
     (1, 'rocketamp'),
     (1, 'robzyb'),
     (1, 'robinhood'),
     (1, 'rlafranchi'),
     (1, 'richf'),
     (1, 'richardfriedman'),
     (1, 'richardbrevig'),
     (1, 'rgcr'),
     (1, 'rfotino'),
     (1, 'reuven'),
     (1, 'remy_'),
     (1, 'reedk'),
     (1, 'redzer'),
     (1, 'redox_'),
     (1, 'redigestit'),
     (1, 'realsimoburns'),
     (1, 'realgrantthomas'),
     (1, 'razor'),
     (1, 'raviojha'),
     (1, 'ravimevcha'),
     (1, 'rathoreabhishek'),
     (1, 'raphastraat'),
     (1, 'ramadis'),
     (1, 'rahulmfg'),
     (1, 'qzervaas'),
     (1, 'qmathe'),
     (1, 'qhoang09'),
     (1, 'pw'),
     (1, 'putsjoe'),
     (1, 'purpleidea'),
     (1, 'psy_'),
     (1, 'psior'),
     (1, 'pschanely'),
     (1, 'projproj'),
     (1, 'programmer01'),
     (1, 'prodpicks'),
     (1, 'prodicus'),
     (1, 'prggmr'),
     (1, 'pouria3'),
     (1, 'populacesoho'),
     (1, 'polyvertex'),
     (1, 'pointum'),
     (1, 'pnwhyc'),
     (1, 'pmoon00'),
     (1, 'plurch'),
     (1, 'plicense'),
     (1, 'plegros'),
     (1, 'pjdorrell'),
     (1, 'pjbrow'),
     (1, 'piotr-j'),
     (1, 'pili'),
     (1, 'pieterhg'),
     (1, 'pierrefar'),
     (1, 'pierre'),
     (1, 'phpnode'),
     (1, 'philwall192'),
     (1, 'philip1209'),
     (1, 'phenomnominal'),
     (1, 'phaser'),
     (1, 'peterwallhead'),
     (1, 'petermolyneux'),
     (1, 'pedro93'),
     (1, 'pavelludiq'),
     (1, 'paulnechifor'),
     (1, 'partywithalocal'),
     (1, 'parolu'),
     (1, 'palferrari'),
     (1, 'palerdot'),
     (1, 'paekut'),
     (1, 'owenfar'),
     (1, 'overcast'),
     (1, 'oso2k'),
     (1, 'osetinsky'),
     (1, 'orf'),
     (1, 'orangepenguin'),
     (1, 'opticalflow'),
     (1, 'onuryilmaz'),
     (1, 'onqtam'),
     (1, 'onli'),
     (1, 'omnidan'),
     (1, 'omidfi'),
     (1, 'olav'),
     (1, 'olalonde'),
     (1, 'ola'),
     (1, 'ohsik'),
     (1, 'ogezi'),
     (1, 'nyddle'),
     (1, 'nvzqz'),
     (1, 'ntumlin'),
     (1, 'nsigma'),
     (1, 'nschoe'),
     (1, 'nsaje'),
     (1, 'novaleaf'),
     (1, 'nonrecursive'),
     (1, 'no_gravity'),
     (1, 'nkristoffersen'),
     (1, 'nk-'),
     (1, 'njrc9'),
     (1, 'nikhildaga'),
     (1, 'niftylettuce'),
     (1, 'nicole_express'),
     (1, 'nicktikhonov'),
     (1, 'nicksmithr'),
     (1, 'nextrevision'),
     (1, 'nevster'),
     (1, 'neverstopcoding'),
     (1, 'netheril96'),
     (1, 'neogenix'),
     (1, 'neilellis'),
     (1, 'nbadg'),
     (1, 'nav'),
     (1, 'nathantross'),
     (1, 'nathanliu09'),
     (1, 'nate'),
     (1, 'natashabaker'),
     (1, 'namuol'),
     (1, 'naikapa'),
     (1, 'nagrom42'),
     (1, 'mzucker'),
     (1, 'myappincome'),
     (1, 'mxstbr'),
     (1, 'mvrekic'),
     (1, 'mvila'),
     (1, 'mvclark'),
     (1, 'muxlab'),
     (1, 'musicnrd'),
     (1, 'muh0m0rka'),
     (1, 'mthomasb'),
     (1, 'mstipetic'),
     (1, 'mrtsepelev'),
     (1, 'mrry'),
     (1, 'mrjn'),
     (1, 'motherwhale'),
     (1, 'morisy'),
     (1, 'morgenkaffee'),
     (1, 'mohebifar'),
     (1, 'mohamedbassem'),
     (1, 'mofle'),
     (1, 'mledunne'),
     (1, 'mklopets'),
     (1, 'mkirsche'),
     (1, 'mkalygin'),
     (1, 'mkagenius'),
     (1, 'mjdesa'),
     (1, 'mjaneczek'),
     (1, 'mjackson'),
     (1, 'mitocgroup'),
     (1, 'mitjap'),
     (1, 'miriadis'),
     (1, 'mipmap04'),
     (1, 'mingabunga'),
     (1, 'minbrowser'),
     (1, 'miller_joe'),
     (1, 'milge'),
     (1, 'mikkiq'),
     (1, 'mikeyanderson'),
     (1, 'mikeem'),
     (1, 'mijustin'),
     (1, 'michelkarma'),
     (1, 'michaelsitver'),
     (1, 'michaeloblak'),
     (1, 'mholt'),
     (1, 'mhluska'),
     (1, 'mhlavacka'),
     (1, 'mgalka'),
     (1, 'mettamage'),
     (1, 'metatask'),
     (1, 'meredydd'),
     (1, 'mengjiang'),
     (1, 'mekanics-2'),
     (1, 'mejamiewilson'),
     (1, 'megalodon'),
     (1, 'medvednikov'),
     (1, 'medv'),
     (1, 'mediasans'),
     (1, 'mechazawa'),
     (1, 'mcbetz'),
     (1, 'mburkon'),
     (1, 'mbosch'),
     (1, 'matthiaswh'),
     (1, 'matthewbauer'),
     (1, 'mattdesl'),
     (1, 'mattbates25'),
     (1, 'matryer'),
     (1, 'massivedragon'),
     (1, 'marmelroy'),
     (1, 'marktangotango'),
     (1, 'markbnj'),
     (1, 'mardurhack'),
     (1, 'marciojmo'),
     (1, 'marcc'),
     (1, 'marbemac'),
     (1, 'mapmeld'),
     (1, 'malleablebyte'),
     (1, 'malcolmocean'),
     (1, 'makpy'),
     (1, 'mailroof'),
     (1, 'madidi707'),
     (1, 'madbitties'),
     (1, 'mackross'),
     (1, 'lyle_nel'),
     (1, 'luxpir'),
     (1, 'lukezli'),
     (1, 'lukethomas'),
     (1, 'lukep423'),
     (1, 'ludwigvan'),
     (1, 'lpellegr'),
     (1, 'lostmsu'),
     (1, 'longnguyen'),
     (1, 'loadfocus'),
     (1, 'ljw1001'),
     (1, 'ljensen'),
     (1, 'ljan'),
     (1, 'liongate2'),
     (1, 'limedaring'),
     (1, 'lillukka'),
     (1, 'lichtschlag'),
     (1, 'liammclennan'),
     (1, 'lgse'),
     (1, 'lewq'),
     (1, 'levng'),
     (1, 'levlaz'),
     (1, 'lev-miseri'),
     (1, 'lerax'),
     (1, 'lefnire'),
     (1, 'leeseibert'),
     (1, 'leesalminen'),
     (1, 'leeab'),
     (1, 'ldom22'),
     (1, 'ldenoue'),
     (1, 'lcllcl'),
     (1, 'lazyfunctor'),
     (1, 'latkin'),
     (1, 'latitude'),
     (1, 'lasercar'),
     (1, 'larion1'),
     (1, 'ladino'),
     (1, 'l8again'),
     (1, 'kyasui'),
     (1, 'krystianszastok'),
     (1, 'kristoiv'),
     (1, 'kristaps1990'),
     (1, 'krat0sprakhar'),
     (1, 'kpsychwave'),
     (1, 'kovetskiy'),
     (1, 'koi'),
     (1, 'kogir'),
     (1, 'knncreative'),
     (1, 'kmf'),
     (1, 'killing_time'),
     (1, 'kikowi'),
     (1, 'kienankb'),
     (1, 'kidstrack'),
     (1, 'kidproquo'),
     (1, 'kiberstranier'),
     (1, 'khirviko'),
     (1, 'khc'),
     (1, 'kfihihc'),
     (1, 'kevining'),
     (1, 'ketharsis'),
     (1, 'kelukelugames'),
     (1, 'kelu124'),
     (1, 'kaydo_com_au'),
     (1, 'karanjthakkar'),
     (1, 'karam'),
     (1, 'kamilszybalski'),
     (1, 'jwmoz'),
     (1, 'justspamjustin'),
     (1, 'julienmarie'),
     (1, 'juiced'),
     (1, 'jsvine'),
     (1, 'jsnider3'),
     (1, 'jshchnz'),
     (1, 'jscampbell05'),
     (1, 'js4'),
     (1, 'jpatapoff'),
     (1, 'jownwayne'),
     (1, 'joslin01'),
     (1, 'joshsharp'),
     (1, 'joshanthony'),
     (1, 'joshaharonoff'),
     (1, 'jorgesanzpe'),
     (1, 'jorgemf'),
     (1, 'johnwheeler'),
     (1, 'johnsan'),
     (1, 'johne20'),
     (1, 'johndavi'),
     (1, 'jmugan'),
     (1, 'jmquigs'),
     (1, 'jmort'),
     (1, 'jmopr'),
     (1, 'jmelett'),
     (1, 'jlft'),
     (1, 'jldupont'),
     (1, 'jkuria'),
     (1, 'jisagigi'),
     (1, 'jimant'),
     (1, 'jiggity'),
     (1, 'jicooo'),
     (1, 'jharsman'),
     (1, 'jftuga'),
     (1, 'jessegrosjean'),
     (1, 'jeromedl'),
     (1, 'jereme'),
     (1, 'jenandre'),
     (1, 'jeffpeiyt'),
     (1, 'jeffcole'),
     (1, 'jdorfman'),
     (1, 'jdmoreira'),
     (1, 'jdeng'),
     (1, 'jcxplorer'),
     (1, 'jblesage'),
     (1, 'jbaviat'),
     (1, 'jatins'),
     (1, 'jasoncrawford'),
     (1, 'jart'),
     (1, 'japhyr'),
     (1, 'jan_g'),
     (1, 'jameswilsterman'),
     (1, 'james_fairhurst'),
     (1, 'jakemor'),
     (1, 'jakegarelick'),
     (1, 'jaip'),
     (1, 'jadebyfield89'),
     (1, 'jacobxi'),
     (1, 'jackwreid'),
     (1, 'jackbwheeler'),
     (1, 'ivshti'),
     (1, 'ivm'),
     (1, 'iuliia_shevchuk'),
     (1, 'itimetrack'),
     (1, 'ishu3101'),
     (1, 'introvertmac'),
     (1, 'interwho'),
     (1, 'inian'),
     (1, 'indutny'),
     (1, 'in4lio'),
     (1, 'imakesoft'),
     (1, 'ilozinski'),
     (1, 'iliaznk'),
     (1, 'igauravsehrawat'),
     (1, 'iatek'),
     (1, 'ianes'),
     (1, 'iamunr'),
     (1, 'iamspoilt'),
     (1, 'iamgordx'),
     (1, 'i_dont_know_'),
     (1, 'iCHAIT'),
     (1, 'hyperknot'),
     (1, 'hwdsl2'),
     (1, 'hvd'),
     (1, 'huxingyi'),
     (1, 'huskyr'),
     (1, 'hrs'),
     (1, 'hpoydar'),
     (1, 'hmppark7'),
     (1, 'highjohn'),
     (1, 'highace'),
     (1, 'hexadecimal'),
     (1, 'hellowub'),
     (1, 'hellobox'),
     (1, 'hden'),
     (1, 'hboon'),
     (1, 'hbbio'),
     (1, 'hardmaru'),
     (1, 'handsontv'),
     (1, 'h_o'),
     (1, 'h8liu'),
     (1, 'gyoungberg'),
     (1, 'gusmd'),
     (1, 'guillaumec'),
     (1, 'guiCoder'),
     (1, 'grvcoelho'),
     (1, 'grimmfang'),
     (1, 'grigio'),
     (1, 'greglindahl'),
     (1, 'gregalbritton'),
     (1, 'grandrew'),
     (1, 'goughjustin'),
     (1, 'gotzephyr'),
     (1, 'gonedo'),
     (1, 'gohighbrow'),
     (1, 'gloubibou'),
     (1, 'gionn'),
     (1, 'gichuru'),
     (1, 'ghinda'),
     (1, 'ggreer'),
     (1, 'gesteves'),
     (1, 'genejaelee'),
     (1, 'gautamkrishnar'),
     (1, 'ganeshkrishnan'),
     (1, 'gammafication'),
     (1, 'futhey'),
     (1, 'ftransformer'),
     (1, 'ftomassetti'),
     (1, 'franzunix'),
     (1, 'franciscop'),
     (1, 'foxhedgehog'),
     (1, 'fouadallaoui'),
     (1, 'forgetcolor'),
     (1, 'foopod'),
     (1, 'fonziguy'),
     (1, 'fomojola'),
     (1, 'fogleman'),
     (1, 'flannelhead'),
     (1, 'fka'),
     (1, 'filiptc'),
     (1, 'filipedeschamps'),
     (1, 'fibbery'),
     (1, 'ffunenga'),
     (1, 'feelix'),
     (1, 'fdim'),
     (1, 'fastbrick'),
     (1, 'fahimulhaq'),
     (1, 'fagnerbrack'),
     (1, 'ezioamf'),
     (1, 'ex3ndr'),
     (1, 'evanmarks'),
     (1, 'etrackr'),
     (1, 'ethan_sutin'),
     (1, 'eskibars'),
     (1, 'ers35'),
     (1, 'erroneousboat'),
     (1, 'eric-flw'),
     (1, 'epagamer'),
     (1, 'eoinmurray92'),
     (1, 'engintekin'),
     (1, 'engcrunch'),
     (1, 'engageperpage'),
     (1, 'enan'),
     (1, 'emily_b'),
     (1, 'eliotpeper'),
     (1, 'elbrujohalcon'),
     (1, 'elayabharath'),
     (1, 'ekinertac'),
     (1, 'ejholmes'),
     (1, 'eivarv'),
     (1, 'eibrahim'),
     (1, 'eharbaugh'),
     (1, 'egraether'),
     (1, 'eg312'),
     (1, 'efkv'),
     (1, 'efesak2'),
     (1, 'edjboston'),
     (1, 'ed'),
     (1, 'ecesena'),
     (1, 'dzjosjusuns'),
     (1, 'dxdstudio'),
     (1, 'duosearch'),
     (1, 'dukoid'),
     (1, 'ducuboy'),
     (1, 'dtougas'),
     (1, 'dtertman'),
     (1, 'dstik'),
     (1, 'drusenko'),
     (1, 'dropfrog'),
     (1, 'drizzzler'),
     (1, 'drizin'),
     (1, 'driusan'),
     (1, 'drewminns'),
     (1, 'drakenot'),
     (1, 'dosh'),
     (1, 'dorianm'),
     (1, 'dmmalam'),
     (1, 'dirwiz'),
     (1, 'dignite'),
     (1, 'digitalice'),
     (1, 'digital_ins'),
     (1, 'diegoloop'),
     (1, 'dhotson'),
     (1, 'deviloflaplace'),
     (1, 'deutronium'),
     (1, 'derhuerst'),
     (1, 'delsol'),
     (1, 'delan'),
     (1, 'dejan'),
     (1, 'deadfece'),
     (1, 'dc17'),
     (1, 'dberg'),
     (1, 'dawidpacha'),
     (1, 'davidsQL'),
     (1, 'davewongillies'),
     (1, 'davebryand'),
     (1, 'datamantle'),
     (1, 'data37'),
     (1, 'dassreis'),
     (1, 'darrenbuckner'),
     (1, 'daoudat'),
     (1, 'dany74q'),
     (1, 'danpaz'),
     (1, 'danielrh'),
     (1, 'danielhunt'),
     (1, 'danieka'),
     (1, 'dandelany'),
     (1, 'damjanstankovic'),
     (1, 'damienj'),
     (1, 'daliwali'),
     (1, 'daisyegeolu'),
     (1, 'cyberferret'),
     (1, 'curryhoward'),
     (1, 'curator'),
     (1, 'cundd'),
     (1, 'cubebrush'),
     (1, 'ctex'),
     (1, 'crrashby'),
     (1, 'crispytx'),
     (1, 'crisen'),
     (1, 'crifei93'),
     (1, 'creichert'),
     (1, 'craine'),
     (1, 'craigm26'),
     (1, 'craig'),
     (1, 'crad'),
     (1, 'cqcn1991'),
     (1, 'couac'),
     (1, 'conorpp'),
     (1, 'colorcontrast'),
     (1, 'cm2012'),
     (1, 'cloud-rail'),
     (1, 'clmcleod'),
     (1, 'cjkarr'),
     (1, 'cjdell'),
     (1, 'ciconia'),
     (1, 'chvid'),
     (1, 'chrstphrhrt'),
     (1, 'chrisbuttenham'),
     (1, 'chrift'),
     (1, 'chr15m'),
     (1, 'chintan'),
     (1, 'chemouna'),
     (1, 'cheeaun'),
     (1, 'chasefinch'),
     (1, 'changeagain_me'),
     (1, 'cha-cho'),
     (1, 'cezarfloroiu'),
     (1, 'ceyhunkazel'),
     (1, 'cdubzzz'),
     (1, 'cdsmarty'),
     (1, 'ccvannorman'),
     (1, 'caspg'),
     (1, 'carmenapostu'),
     (1, 'carbeewo'),
     (1, 'capybara'),
     (1, 'captainbenises'),
     (1, 'candresbolanos'),
     (1, 'camelaissani'),
     (1, 'cam_pj'),
     (1, 'callmevlad'),
     (1, 'callenq'),
     (1, 'caleblloyd'),
     (1, 'cagataygurturk'),
     (1, 'cabinguy'),
     (1, 'bryanbraun'),
     (1, 'brilliantsob'),
     (1, 'brian-armstrong'),
     (1, 'brensudol'),
     (1, 'brandonlipman'),
     (1, 'brandly'),
     (1, 'brahnema'),
     (1, 'bpesquet'),
     (1, 'bouke'),
     (1, 'borge'),
     (1, 'booruguru'),
     (1, 'bookerio'),
     (1, 'bluetomcat'),
     (1, 'bluejekyll'),
     (1, 'blubbi2'),
     (1, 'blisse'),
     (1, 'bkazemi'),
     (1, 'billowycoat'),
     (1, 'bgrgndz'),
     (1, 'bertelet'),
     (1, 'benwilber0'),
     (1, 'benjamsmith'),
     (1, 'beneills'),
     (1, 'bendmorris'),
     (1, 'ben174'),
     (1, 'bemaniac'),
     (1, 'beliu'),
     (1, 'beartear'),
     (1, 'bd82'),
     (1, 'bburshteyn'),
     (1, 'bbilko'),
     (1, 'bbayer'),
     (1, 'bbauman'),
     (1, 'bass_case'),
     (1, 'barisser'),
     (1, 'barbarian'),
     (1, 'bakli'),
     (1, 'badgerops'),
     (1, 'b44rd'),
     (1, 'b2library'),
     (1, 'axelut'),
     (1, 'avolcano'),
     (1, 'avinassh'),
     (1, 'aviaviavi'),
     (1, 'atomashpolskiy'),
     (1, 'atix-co'),
     (1, 'aschearer'),
     (1, 'arunmoezhi'),
     (1, 'artagnon'),
     (1, 'arnauddri'),
     (1, 'arisAlexis'),
     (1, 'ariestiyansyah'),
     (1, 'arekkas'),
     (1, 'arciini'),
     (1, 'archagon'),
     (1, 'ar7hur'),
     (1, 'appstarter'),
     (1, 'apineda'),
     (1, 'apancyborg'),
     (1, 'aorioli'),
     (1, 'antrion'),
     (1, 'antonycourtney'),
     (1, 'anpur'),
     (1, 'anoopmunshi'),
     (1, 'anonfunction'),
     (1, 'andy112'),
     (1, 'andronik'),
     (1, 'andrewworsfold'),
     (1, 'andrewfromx'),
     (1, 'andres'),
     (1, 'andrerpena'),
     (1, 'andreas-schrade'),
     (1, 'andreareginato'),
     (1, 'andikan'),
     (1, 'anacleto'),
     (1, 'amzans'),
     (1, 'amasad'),
     (1, 'allenguo'),
     (1, 'alistproducer2'),
     (1, 'ali_ibrahim'),
     (1, 'alexkern'),
     (1, 'alexkehr'),
     (1, 'alexggordon'),
     (1, 'alexeykudinkin'),
     (1, 'alex_g'),
     (1, 'alex2401'),
     (1, 'alessiosantocs'),
     (1, 'ajwinn'),
     (1, 'ajeet_dhaliwal'),
     (1, 'airconsole'),
     (1, 'ahl'),
     (1, 'agp2572'),
     (1, 'afshinmeh'),
     (1, 'afshin'),
     (1, 'afaik'),
     (1, 'aeto'),
     (1, 'adzicg'),
     (1, 'adrianpike'),
     (1, 'adrianmacneil'),
     (1, 'adnanh'),
     (1, 'adius'),
     (1, 'adansdpc'),
     (1, 'adamhayek'),
     (1, 'adamcath'),
     (1, 'adambutler'),
     (1, 'adambrod'),
     (1, 'adam_tavener'),
     (1, 'acrosync'),
     (1, 'acmeyer9'),
     (1, 'achillean'),
     (1, 'achalkley'),
     (1, 'acemtp'),
     (1, 'acconrad'),
     (1, 'abrena'),
     (1, 'abhiyerra'),
     (1, 'abhishekdesai'),
     (1, 'aaronrandall'),
     (1, 'a_t'),
     (1, 'a_burns'),
     (1, '_yesmeck'),
     (1, '_air'),
     (1, 'Zojuba'),
     (1, 'Zezima'),
     (1, 'ZenSwordArts'),
     (1, 'ZaneClaes'),
     (1, 'Xeoncross'),
     (1, 'Uptrenda'),
     (1, 'TruffleMuffin'),
     (1, 'TazeTSchnitzel'),
     (1, 'Swizec'),
     (1, 'Sventek'),
     (1, 'StevePerkins'),
     (1, 'Solomonrajput1'),
     (1, 'Snocrash'),
     (1, 'Snaipe'),
     (1, 'Sir_Cmpwn'),
     (1, 'ScottAS'),
     (1, 'SamWhited'),
     (1, 'SRasch'),
     (1, 'RoxasShadow'),
     (1, 'RizkSaade'),
     (1, 'Rican7'),
     (1, 'RazTerr'),
     (1, 'ROFISH'),
     (1, 'RADSR'),
     (1, 'Pathshare'),
     (1, 'OrangeFlash81'),
     (1, 'Nilef'),
     (1, 'Nikolas0'),
     (1, 'NeutrinoAPI'),
     (1, 'NetStrikeForce'),
     (1, 'Narutu'),
     (1, 'NFicano'),
     (1, 'Muted'),
     (1, 'MrAwesomeSauce'),
     (1, 'MonkeyDan'),
     (1, 'Mojah'),
     (1, 'MoD411'),
     (1, 'MironV'),
     (1, 'Meekro'),
     (1, 'MaysonL'),
     ...]



In order not to extend too much we will stay with the first 3 in `Ask_post` since the difference between the second and the third is not only 7 points.

    - hoodoof
    - tmaly
    - whoishiring

And we will do the same in `Show_post`however here the difference is not so much between one score and another, just 3 of difference.

    - vipul4vb
    - soheil
    - max0563

Ordered in such a way that the first one is the one who has made the most post.

Now that we know who are the people who write the most, let's see what score they gave them, when they wrote it and what the title is.

###  Number of posts by user.


```python
# ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']

def all_activity_post(name, posts):
    datum_format = "%m/%d/%Y %H:%M"
    time_format = '%Y/%m/%d'
    time = 0
    num_points = 0
    post_published = {}

    print("Score Date           Post ")
    for row in posts:
        if row[5] == name:           # ['author']
            time +=1
            num_points = int(row[3]) # ['num_points']
            tittle = row[1]
            
            ask_date = row[6]       # 'created_at'
            
            ask_date = dt.datetime.strptime(ask_date, datum_format) # date_format = "%m/%d/%Y %H:%M"
            date_object = ask_date.strftime(time_format)           # time_format = '%Y/%m/%d'
            
            post_published = "{points} -- " "{date} " " {tittle},".format(tittle = row[1],
                                                                          date = date_object, points = row[3] )
            print(post_published)
    
    total_number_post = "Total number of post = {n_post}".format(n_post = time)
    print("\n")
    print(total_number_post)
```

### What are the most significant activity by user in `Ask_posts` ?  

- What has been the **activity** in the forum per user? 

- What **score** has each post you created received? 

- **Date** when they did it?

- Title of the post.

- What is the total **number of posts** by user?


```python
all_activity_post('hoodoof', ask_posts)
```

    Score Date           Post 
    3 -- 2016/07/10  Ask HN: Imagine it's 1993  what would you put in an MVP web browser?,
    3 -- 2016/01/11  Ask HN: Can anyone suggest a good RSS newsreader with a set of tech news feeds?,
    3 -- 2016/02/26  Ask HN: Someone is stealing things from my car. What security camera would help?,
    2 -- 2016/09/04  Ask HN: Why should open source support be free? I don't think it should.,
    9 -- 2016/04/12  Ask HN: What's it like working at a cannabis tech startup company?,
    2 -- 2016/04/08  Ask HN: What is the most money a bootstrapped, one-person company has sold for?,
    1 -- 2016/05/20  Ask HN: Are you building something?  How long for? How much longer to go?,
    4 -- 2016/04/07  Ask HN: Why is it still not possible to search an S3 bucket?,
    1 -- 2016/05/04  Ask HN: Why does Etsy have so many items titled DO Not PURCHASE?,
    11 -- 2016/06/03  Ask HN: Is there an up-to-date global index of conferences?,
    2 -- 2016/04/23  Ask HN: What would TV cop drama be about if drugs were legal?,
    4 -- 2016/03/26  Ask HN: With all our software built on so many dependencies, is anything secure?,
    7 -- 2016/04/11  Ask HN: Have you sat in on acquisition discussions? How to ask for a billion $?,
    3 -- 2015/12/03  Ask HN: Can anyone suggest great examples of continuation passing in JavaScript?,
    10 -- 2016/05/16  Ask HN: If you could restart your current project, what would you do different?,
    2 -- 2016/02/28  Ask HN: Could voice commands make programming easier?,
    
    
    Total number of post = 16



```python
all_activity_post('tmaly', ask_posts)
```

    Score Date           Post 
    12 -- 2016/03/21  Ask HN: How do you find unused CSS?,
    3 -- 2015/09/23  Ask HN: Laptop bag for 15 Macbook Pro for inclement weather,
    1 -- 2016/06/22  Ask HN: Operational complexity of micro services?,
    8 -- 2016/01/12  Ask HN: Online learning,
    2 -- 2015/10/20  Ask HN: Wireless HDMI for Macbook to TV,
    3 -- 2015/12/28  Ask HN: Arduino or Raspberry PI for teenager?,
    2 -- 2016/01/15  Ask HN: Website Obesity Crisis,
    1 -- 2015/10/26  Ask HN: Algorithm for Scheduling appointments by location,
    3 -- 2016/08/27  Ask HN: Accuracy of ip to geo location?,
    2 -- 2015/10/30  Ask HN: Feedback on design?,
    2 -- 2016/05/17  Ask HN: What is your favorite podcast episode?,
    2 -- 2016/03/29  Ask HN: Best use case writeups of SOA?,
    1 -- 2016/06/06  Ask HN: Collaborative RSS?,
    2 -- 2016/02/14  Ask HN: Weather site negative temps,
    
    
    Total number of post = 14



```python
all_activity_post('tixocloud', ask_posts)
```

    Score Date           Post 
    5 -- 2016/01/07  Ask HN: Anyone interested in starting an IT consultancy?,
    6 -- 2015/11/09  Ask HN: How do I start an analytics consulting company?,
    1 -- 2016/02/02  Ask HN: Anyone from Malaysia on HN?,
    1 -- 2016/02/03  Ask HN: How do you find early adopters?,
    8 -- 2016/06/08  Ask HN: Would you use an app to keep track of all your relationships?,
    1 -- 2016/06/15  Ask HN: Protecting database information?,
    
    
    Total number of post = 6


### What are the most significant activity by user in `Show_posts`  ?


```python
all_activity_post('vipul4vb', show_posts)
```

    Score Date           Post 
    2 -- 2016/04/04  Show HN: Quantitative user research reveals useful UX observation on LinkedIn,
    1 -- 2016/06/06  Show HN: Import Balsamiq Mockups into CanvasFlip using this simple interface,
    1 -- 2016/04/25  Show HN: Donald Trump V/s Hillary Clinton Better On-Site UX?,
    2 -- 2016/04/06  Show HN: UX Insights on largest e-commerce app  Amazon,
    
    
    Total number of post = 4



```python
all_activity_post('soheil', show_posts)
```

    Score Date           Post 
    1 -- 2016/03/28  Show HN: Get hired as a team: Work with people you know,
    3 -- 2016/02/04  Show HN: Demo: most accurate speech recognition,
    7 -- 2016/03/06  Show HN: Lightweight Twitter for Mac client,
    2 -- 2016/08/22  Show HN: 1) Build Team 2) Interview 3) Offer,
    
    
    Total number of post = 4



```python
all_activity_post('brakmic', show_posts) 
```

    Score Date           Post 
    21 -- 2016/02/27  Show HN: PouchDB Bindings for PureScript,
    3 -- 2016/01/27  Show HN: JSON.stringify (without circular deps) for AngularJS 1.x,
    
    
    Total number of post = 2


### Who has been the most voted, when they was and what was the title of the post?

We note that not all publications have the same value for the community and we see this reflected in the votes they receive, so it would be interesting to know which of the publications that our users made had more votes and therefore which were the most important issues for the community. 


```python
# ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']

def author_most_valuable_post(name, posts):
    datum_format = "%m/%d/%Y %H:%M"
    num_points = 0
    post_published = {}

    print("Score Date           Post ")
    for row in posts:
        if row[5] == name:
            if num_points < int(row[3]):
                num_points = int(row[3])

                tittle = row[1]
                
                time_format = '%Y/%m/%d'
                ask_date = row[6]
                ask_date = dt.datetime.strptime(ask_date, datum_format)
                date_object = ask_date.strftime(time_format)
                
                post_published.setdefault(num_points,[]).append(num_points)
                post_published.setdefault(num_points,[]).append(date_object)
                post_published.setdefault(num_points,[]).append(tittle)
                
    for i in reversed(post_published):
        print(post_published[i])
```

### `Ask_post`


```python
author_most_valuable_post('hoodoof', ask_posts)
```

    Score Date           Post 
    [11, '2016/06/03', 'Ask HN: Is there an up-to-date global index of conferences?']
    [9, '2016/04/12', "Ask HN: What's it like working at a cannabis tech startup company?"]
    [3, '2016/07/10', "Ask HN: Imagine it's 1993  what would you put in an MVP web browser?"]



```python
author_most_valuable_post('tmaly', ask_posts)
```

    Score Date           Post 
    [12, '2016/03/21', 'Ask HN: How do you find unused CSS?']



```python
author_most_valuable_post('tixocloud', ask_posts)
```

    Score Date           Post 
    [8, '2016/06/08', 'Ask HN: Would you use an app to keep track of all your relationships?']
    [6, '2015/11/09', 'Ask HN: How do I start an analytics consulting company?']
    [5, '2016/01/07', 'Ask HN: Anyone interested in starting an IT consultancy?']


### `Show_post`


```python
author_most_valuable_post('vipul4vb', show_posts)
```

    Score Date           Post 
    [2, '2016/04/04', 'Show HN: Quantitative user research reveals useful UX observation on LinkedIn']



```python
author_most_valuable_post('soheil', show_posts)
```

    Score Date           Post 
    [7, '2016/03/06', 'Show HN: Lightweight Twitter for Mac client']
    [3, '2016/02/04', 'Show HN: Demo: most accurate speech recognition']
    [1, '2016/03/28', 'Show HN: Get hired as a team: Work with people you know']



```python
brakmic = author_most_valuable_post('brakmic', show_posts)
```

    Score Date           Post 
    [21, '2016/02/27', 'Show HN: PouchDB Bindings for PureScript']


{{aaa}}

Ranking of Authors by type of post, number of times they have written post, title and rating

|-- post --|-- ranking-- | -- authors --| -- # of post -- | -- Title of most valuable post --- |-- Score -- | -- Date -- |
|:---|:---|: -- |:--- |:-- |:--: |:--:|
|Ask post|1|hoodoof  |16  |Ask HN: Is there an up-to-date global index of conferences?'|11|2016/06/03|
|Ask post|2|tmaly    |14  |'12, '2016/03/21', 'Ask HN: How do you find unused CSS?'|12|2016/06/03|
|Ask post|3|tixocloud|6   |'Ask HN: Would you use an app to keep track of all your relationships?'|8|'2016/01/07'|
|Show post|1|vipul4vb  |4  |'Show HN: Quantitative user research reveals useful UX observation on LinkedIn'|2|'2016/04/04'|
|Show post|2|soheil  |4  |'Ask HN: How do you find unused CSS?'|7|2016/03/06|
|Show post|3|brakmic |2  |'Show HN: PouchDB Bindings for PureScript'|21|'2016/02/27'|

## Some conclusions.

We can see the users separated by those who asked questions in `ask_post` and those who posted in `show_show`.

It is striking that there is no correlation between the fact of writing more and having a better score (at least in `ask_post`) in `show_show` it is almost maintained.

It is also noteworthy that the score received does not correlate with the number of posts they have written.

We see that users write more questions than shows, however the scores received by those who wrote in show are higher than in ask. 

From this we can deduce that this community values sharing information that is useful to the community and types of questions that help its members.
