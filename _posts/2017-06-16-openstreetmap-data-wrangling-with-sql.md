---
layout: post
published: true
title: OpenStreetMap Data Wrangling with SQL
---

OpenStreetMap is a community built free editable map of the world, inspired by the success of Wikipedia where crowdsourced data is open and free from proprietary restricted use. We see some examples of its use by Craigslist and Foursquare, as an open source alternative to Google Maps.

http://www.openstreetmap.org

Users can map things such as polylines of roads, draw polygons of buildings or areas of interest, or insert nodes for landmarks. These map elements can be further tagged with details such as street addresses or amenity type. Map data is stored in an XML format. More details about the OSM XML can be found here:

http://wiki.openstreetmap.org/wiki/OSM_XML

Some highlights of the OSM XML format relevent to this project are:

- OSM XML is list of instances of data primatives (nodes, ways, and relations) found within a given bounds
- nodes represent dimensionless points on the map
- ways contain node references to form either a polyline or polygon on the map
- nodes and ways both contain children tag elements that represent key value pairs of descriptive information about a given node or way



As with any user generated content, there is likely going to be dirty data. In this project I'll attempt to do some auditing, cleaning, and data summarizing tasks with Python and SQL.

#### Map Area: Boston, MA, USA

Data Sources:

https://mapzen.com/data/metro-extracts/metro/boston_massachusetts/

The unzipped data was 415 MB

### Problems Encountered:

After taking a small sample of the dataset using sample_region.py, I used audit.py and postal_code.py to audit the sample data. I noticed following problems:

* Abbreviated street types ('St.', 'St', 'ST', 'Ave', 'Ave.', 'Pkwy', 'Ct', 'Sq', etc).
* Inconsistent postal codes ('MA 02118', '02118-0239')

#### Abbrevated street names

There were a lot of street names where the type of street, e.g. Avenue or Boulevard, was either abbreviated or mispelled. I've corrected those where possible. The following are the steps I took to clean the dataset.

* Create a list of expected street types that do not need to be cleaned.
* The function "audit_street_type()" collects the last words in the "street_name" strings, if they are not partof the expected list, then stores them in the dictionary "street_types". This will allow me to see the nonuniform and abbreviated street types being used and give me a better sense of the data as a whole.
* The "is_street_name()" function looks for tags that specify street names (k="addr:street").
* The "audit()" function returns a dictionary that match the above function conditions.
* The "update_name()" function takes an old name to mapping dictionary, and update to a new one.

I have included code extract from audit.py below.


```python
#This is not complete list of mapping. Please refer data.py

expected = ["Street", "Avenue", "Boulevard", "Drive", "Court", "Place", "Square", "Lane", "Road",
            "Trail", "Parkway", "Commons", "Layout", "Main", "Broadway", "Plaza", "Park"]

mapping = {"St": "Street",
           "st": "Street",
           "ST": "Street",
           "St.": "Street",
           "St,": "Street",
           "Street.": "Street",
           "street": "Street",
           "Sq": "Square",
           "Rd.": "Road",
           "Rd": "Road",
           }
```


```python
#Auditing Street Names

def audit_street_type(street_types, street_name):
    m = street_type_re.search(street_name)
    if m:
        street_type = m.group()
        if street_type not in expected:
            street_types[street_type].add(street_name)


def is_street_name(elem):
    return (elem.attrib['k'] == "addr:street")


def audit(osmfile):
    osm_file = open(osmfile, "r")
    street_types = defaultdict(set)
    for event, elem in ET.iterparse(osm_file, events=("start",)):

        if elem.tag == "node" or elem.tag == "way":
            for tag in elem.iter("tag"):
                if is_street_name(tag):
                    audit_street_type(street_types, tag.attrib['v'])
    osm_file.close()
    return street_types


def update_name(name, mapping):
    for key, value in mapping.iteritems():
        if re.search(key, name):
            name = re.sub(street_type_re, value, name)
        #print name

    return name

```

#### Inconsistent Postal Codes

Boston area postal codes begin with "021, 024 and 022". Some postal codes had five-digits, some had nine-digits, and some started from state characters.
I decided to drop leading and trailing characters before and after the main 5 digit post code. This dropped the state characters "MA" from "MA 02118" and dropped "0239" from "02118-0239" using the following function in postal_code.py.


```python
# Audit Postal codes

def is_postcode(elem):
    return (elem.attrib['k'] == "addr:postcode")

def update_postcode(postcode):
    # new regular expression pattern
    search = re.match(r'^\D*(\d{5}).*', postcode)
    # select the group that is captured
    if search:
        clean_postcode = search.group(1)
        return clean_postcode

def audit_postcode(osmfile):
    post_file = open(osmfile, "r")
    for event, elem in ET.iterparse(post_file, events=("start",)):
        if elem.tag == "node" or elem.tag == "way":
            for tag in elem.iter("tag"):
                if is_postcode(tag):
                    post_code = tag.attrib['v'].strip()
                    #print post_code
                    post_code = update_postcode(post_code)
                    #print post_code
                    #print ""
    post_file.close()
    #return post_code

```

### Prepare for Database - SQL:

The next step is to prepare the data to be inserted into a SQL database using data.py.
To do so I parsed the elements in the OSM XML file, transforming them from document format to tabular format, thus making it possible to write to .csv files. These csv files can then easily be imported to a SQL database as tables.
The "shape_element()" function is used to transform each element in the correct format, I have audited and corrected the Street Names and Postal codes while processing the osm file using process_map() function.

#### Create SQL Database and Tables

I made use of python sqlite3 library to create Database and the required tables. The complete code is available in csv_to_db.py.
I created nodes, nodes_tags, ways, ways_tags, ways_nodes tables and parsed the csv files to fill the respective tables.


```python
import sqlite3
import csv
from pprint import pprint

sqlite_file = 'mydb.db'

# Connect to the database
conn = sqlite3.connect(sqlite_file)

# Get a cursor object
cur = conn.cursor()
```


```python
#Create nodes table
#Before I (re)create the table, I will have to drop the table if it already exists
cur.execute('''DROP TABLE IF EXISTS nodes''')
conn.commit()
cur.execute('''
CREATE TABLE nodes (
    id INTEGER PRIMARY KEY NOT NULL, lat REAL, lon REAL, user TEXT, uid INTEGER, version INTEGER, changeset INTEGER,
    timestamp TEXT)
''')
conn.commit()

# Read in the csv file as a dictionary, format the
# data as a list of tuples:
with open('nodes_tags.csv','rb') as fin:
    dr = csv.DictReader(fin) # comma is default delimiter
    to_db = [(i['id'], i['key'],i['value'].decode("utf-8"), i['type']) for i in dr]

# insert the formatted data
cur.executemany("INSERT INTO nodes_tags(id, key, value,type) VALUES (?, ?, ?, ?);", to_db)
# commit the changes
conn.commit()
```

#### Lets Start exploring the Database:

File size:

mydb.db . . . . . . . . . . 235 MB


```python
# Number of nodes

cur.execute("SELECT COUNT(*) FROM nodes;") 
pprint("Number of nodes")
pprint(cur.fetchall())
```

    'Number of nodes'
    [(1937402,)]
    


```python
# Number of ways

cur.execute("SELECT COUNT(*) FROM ways;")
pprint("Number of ways")
pprint(cur.fetchall())
```

    'Number of ways'
    [(310052,)]
    


```python
# Number of unique users

cur.execute("SELECT COUNT(DISTINCT(e.uid)) \
FROM (SELECT uid FROM nodes UNION ALL SELECT uid FROM ways) e;") 
pprint("Number of unique users")
pprint(cur.fetchall())
```

    'Number of unique users'
    [(1362,)]
    


```python
# Top 10 contributing users


cur.execute("SELECT e.user, COUNT(*) as num \
FROM (SELECT user FROM nodes UNION ALL SELECT user FROM ways) e \
GROUP BY e.user \
ORDER BY num DESC \
LIMIT 10")
pprint("Top 10 contributing users")
pprint(cur.fetchall())
```

    'Top 10 contributing users'
    [(u'crschmidt', 1201830),
     (u'jremillard-massgis', 429298),
     (u'OceanVortex', 91941),
     (u'wambag', 79913),
     (u'morganwahl', 69262),
     (u'ryebread', 66521),
     (u'MassGIS Import', 63040),
     (u'ingalls_imports', 32453),
     (u'Ahlzen', 27110),
     (u'mapper999', 14740)]
    


```python
# Top 10 common amenities in Boston 

cur.execute("SELECT value, COUNT(*) as num \
FROM nodes_tags \
WHERE key='amenity' \
GROUP BY value \
ORDER BY num DESC \
LIMIT 10;")
pprint("Top 10 common amenities in Boston")
pprint(cur.fetchall())
```

    'Top 10 common amenities in Boston'
    [(u'bench', 1064),
     (u'restaurant', 664),
     (u'school', 508),
     (u'bicycle_parking', 280),
     (u'library', 278),
     (u'place_of_worship', 275),
     (u'cafe', 267),
     (u'fast_food', 191),
     (u'bicycle_rental', 138),
     (u'post_box', 121)]
    


```python
# Top 10 popular cuisines 

cur.execute("SELECT nodes_tags.value, COUNT(*) as num \
FROM nodes_tags \
    JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='restaurant') i \
    ON nodes_tags.id=i.id \
WHERE nodes_tags.key='cuisine' \
GROUP BY nodes_tags.value \
ORDER BY num DESC \
LIMIT 10;")
pprint("Top 10 popular cuisines")
pprint(cur.fetchall())
```

    'Top 10 popular cuisines'
    [(u'pizza', 42),
     (u'american', 36),
     (u'chinese', 31),
     (u'italian', 31),
     (u'mexican', 30),
     (u'indian', 22),
     (u'thai', 19),
     (u'asian', 15),
     (u'japanese', 14),
     (u'regional', 12)]
    


```python
# Number of Book stores

cur.execute("select count(*) from nodes_tags where key='shop' and value='books';")
pprint("Number of Book stores")
pprint(cur.fetchall())
```

    'Number of Book stores'
    [(25,)]
    


```python
# Top 5 popular cafes

cur.execute("SELECT nodes_tags.value, COUNT(*) as num FROM nodes_tags JOIN (SELECT DISTINCT(id) \
             FROM nodes_tags WHERE value='cafe') i ON nodes_tags.id=i.id WHERE nodes_tags.key='name' \
             GROUP BY nodes_tags.value ORDER BY num DESC LIMIT 5;")
pprint("Top 5 popular cafes")
pprint(cur.fetchall())
```

    'Top 5 popular cafes'
    [(u'Starbucks', 48),
     (u"Dunkin' Donuts", 41),
     (u'Au Bon Pain', 7),
     (u"Peet's Coffee", 5),
     (u'Starbucks Coffee', 5)]
    


```python
# Top 5 most occuring banks

cur.execute("SELECT nodes_tags.value, COUNT(*) as num \
        FROM nodes_tags \
            JOIN (SELECT DISTINCT(id) FROM nodes_tags WHERE value='bank') i \
            ON nodes_tags.id=i.id \
        WHERE nodes_tags.key='name' \
        GROUP BY nodes_tags.value \
        ORDER BY num DESC \
        LIMIT 5;")
pprint("Top 5 most occuring banks")
pprint(cur.fetchall())
```

    'Top 5 most occuring banks'
    [(u'Bank of America', 14),
     (u'Citizens Bank', 10),
     (u'Santander', 7),
     (u'Eastern Bank', 5),
     (u'TD Bank', 5)]
    

### Additional Ideas for Improvement and Anticipated Problems :

After the above review and analysis, it is obvious that the Boston area is not complete and accurate, though I believe street names and post codes have been cleaned for the purpose of this exercise. For example from above queries, "Starbucks" appeared twice in the same list. Having a look at sample dataset of Boston, I found that Phone no. are not uniformly formatted. This can be taken as further cleaning task.



```python
cur.execute("SELECT * FROM nodes_tags where key='phone' limit 318, 10;") 
pprint("Number of nodes")
pprint(cur.fetchall())
```

    'Number of nodes'
    [(1831393836, u'phone', u'+1-617-492-7021', u'regular'),
     (1842171157, u'phone', u'617-294-6063', u'regular'),
     (1851602965, u'phone', u'+1 617 876 3076', u'regular'),
     (1907895940, u'phone', u'+1 617 864 3400', u'regular'),
     (1914291499, u'phone', u'617.354.4200', u'regular'),
     (1914343564, u'phone', u'617-576-3000', u'regular'),
     (1934348666, u'phone', u'617-269-0110', u'regular'),
     (1934348672, u'phone', u'(617) 436-2786', u'regular'),
     (1934348674, u'phone', u'(617) 825-6180', u'regular'),
     (1936217501, u'phone', u'+1 781 3951600', u'regular')]
    

To fix the Phone numbers in the current map we can use a parser correct all the phone numbers. Going forward we can have field validation for fields like postal codes and phone nos. so they adhere to standard format as per country/region. We can also suggest the common places to the editors while someone is updating/adding new info to the map. For example if a person is adding info about "Starbucks" and the person is misspelling it as "Star Bucks", then the suggestions can avoid such mistakes.

Biggest challenge is keeping maps up-to-date, since just like Wikepedia, Openstreetmaps depends on its geographically diverse user-base due to emphasis of local knowledge . We can leverage the power of Machine Learning/Deep Learning to our use. I found this blog post by google helpful https://research.googleblog.com/2017/05/updating-google-maps-with-deep-learning.html



### References:

* For most of the Programming I took help of https://Stackoverflow.com
* For SQL I referred https://www.w3schools.com/sql/default.asp
