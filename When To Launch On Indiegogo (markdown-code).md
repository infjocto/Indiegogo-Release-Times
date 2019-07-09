
# When Should I Launch My Indiegogo Campaign?
July 8, 2019

# 1. Dataset Preparation

## 1.1. Load Dataset & Install Packages


```python
import pandas as pd
import numpy as np 
```

Note: If you are having trouble installing packages, double check your directory settings. https://www.youtube.com/watch?v=237dNNQhD3Q

### About The Data:
- 16 monthly datasets from January 2018 to May 2019
- October 2018 data is missing

I omitted campaigns webscraped in 2017 and earlier since they did not contain date & time data (different webpage design).

Data can be found at: https://webrobots.io/indiegogo-dataset/


```python
# Combining the 16 datasets into 1

# 2019
projects = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo05_19.csv", parse_dates = ["open_date", "close_date"])
projects04_19 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo04_19.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects04_19, ignore_index = True)
projects03_19 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo03_19.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects03_19, ignore_index = True)
projects02_19 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo02_19.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects02_19, ignore_index = True)
projects01_19 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo01_19.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects01_19, ignore_index = True)
# 2018
projects12_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo12_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects12_18, ignore_index = True)
projects11_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo11_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects11_18, ignore_index = True)
projects09_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo09_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects09_18, ignore_index = True)
projects08_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo08_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects08_18, ignore_index = True)
projects07_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo07_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects07_18, ignore_index = True)
projects06_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo06_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects06_18, ignore_index = True)
projects05_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo05_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects05_18, ignore_index = True)
projects04_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo04_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects04_18, ignore_index = True)
projects03_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo03_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects03_18, ignore_index = True)
projects02_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo02_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects02_18, ignore_index = True)
projects01_18 = pd.read_csv(r"\Users\onajj\Desktop\Project Files\Indiegogo Crowdfunding\Indiegogo01_18.csv", parse_dates = ["open_date", "close_date"])
projects = projects.append(projects01_18, ignore_index = True)
```

## 1b. Understanding The Data


```python
# Looking at the variables in the dataset and corresponding null values

print ("Total Projects: ", projects.shape[0], "\nTotal Features: ", projects.shape[1])
print(projects.info())
```

    Total Projects:  347606 
    Total Features:  25
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 347606 entries, 0 to 347605
    Data columns (total 25 columns):
    bullet_point            1781 non-null object
    category                347606 non-null object
    category_url            347606 non-null object
    clickthrough_url        347606 non-null object
    close_date              341753 non-null datetime64[ns]
    currency                347606 non-null object
    funds_raised_amount     343341 non-null float64
    funds_raised_percent    343341 non-null float64
    image_url               347597 non-null object
    is_indemand             347606 non-null bool
    is_pre_launch           307287 non-null object
    offered_by              168 non-null object
    open_date               341723 non-null datetime64[ns]
    perk_goal_percentage    25 non-null float64
    perks_claimed           80981 non-null float64
    price_offered           4265 non-null float64
    price_retail            4265 non-null float64
    product_id              4265 non-null float64
    product_stage           35272 non-null object
    project_id              347438 non-null float64
    project_type            347606 non-null object
    source_url              347606 non-null object
    tagline                 347108 non-null object
    tags                    221474 non-null object
    title                   347407 non-null object
    dtypes: bool(1), datetime64[ns](2), float64(8), object(14)
    memory usage: 64.0+ MB
    None
    

Since we want to figure out when to launch and for how long, we are most interested in the time-related variables such as 
- campaign start date ("open_date") and end date ("close_date")
- duration of campaign
- campaign launch time

In addition, these following variables will be needed:
- project id ("project_id")
- "category"
- percentage of goal amount met "funds_raised_percent"
- amount fundraised "funds_raised_amount"
- "currency"


```python
# Sorting projects by project_id
projects = projects.sort_values("funds_raised_amount", ascending = False).sort_values("project_id")
projects.head()
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
      <th>bullet_point</th>
      <th>category</th>
      <th>category_url</th>
      <th>clickthrough_url</th>
      <th>close_date</th>
      <th>currency</th>
      <th>funds_raised_amount</th>
      <th>funds_raised_percent</th>
      <th>image_url</th>
      <th>is_indemand</th>
      <th>...</th>
      <th>price_offered</th>
      <th>price_retail</th>
      <th>product_id</th>
      <th>product_stage</th>
      <th>project_id</th>
      <th>project_type</th>
      <th>source_url</th>
      <th>tagline</th>
      <th>tags</th>
      <th>title</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>329301</th>
      <td>NaN</td>
      <td>Art</td>
      <td>/explore/art</td>
      <td>/projects/jumpingbook</td>
      <td>2010-06-07 06:59:00</td>
      <td>USD</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>https://c1.iggcdn.com/indiegogo-media-prod-cld...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3036.0</td>
      <td>campaign</td>
      <td>https://www.indiegogo.com/explore/all?project_...</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>NaN</td>
      <td>jumpingbook</td>
    </tr>
    <tr>
      <th>45829</th>
      <td>NaN</td>
      <td>Art</td>
      <td>/explore/art</td>
      <td>/projects/jumpingbook</td>
      <td>2010-06-07 06:59:00</td>
      <td>USD</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>https://c1.iggcdn.com/indiegogo-media-prod-cld...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3036.0</td>
      <td>campaign</td>
      <td>https://www.indiegogo.com/explore/all?project_...</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>["other"]</td>
      <td>jumpingbook</td>
    </tr>
    <tr>
      <th>73628</th>
      <td>NaN</td>
      <td>Art</td>
      <td>/explore/art</td>
      <td>/projects/jumpingbook</td>
      <td>2010-06-07 06:59:00</td>
      <td>USD</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>https://c1.iggcdn.com/indiegogo-media-prod-cld...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3036.0</td>
      <td>campaign</td>
      <td>https://www.indiegogo.com/explore/all?project_...</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>["other"]</td>
      <td>jumpingbook</td>
    </tr>
    <tr>
      <th>32775</th>
      <td>NaN</td>
      <td>Art</td>
      <td>/explore/art</td>
      <td>/projects/jumpingbook</td>
      <td>2010-06-07 06:59:00</td>
      <td>USD</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>https://c1.iggcdn.com/indiegogo-media-prod-cld...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3036.0</td>
      <td>campaign</td>
      <td>https://www.indiegogo.com/explore/all?project_...</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>["other"]</td>
      <td>jumpingbook</td>
    </tr>
    <tr>
      <th>286439</th>
      <td>NaN</td>
      <td>Art</td>
      <td>/explore/art</td>
      <td>/projects/jumpingbook</td>
      <td>2010-06-07 06:59:00</td>
      <td>USD</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>https://c1.iggcdn.com/indiegogo-media-prod-cld...</td>
      <td>False</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3036.0</td>
      <td>campaign</td>
      <td>https://www.indiegogo.com/explore/all?project_...</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>NaN</td>
      <td>jumpingbook</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 25 columns</p>
</div>



As you can see, we have duplicates. This leads us to the next section: Data Cleaning

## 1c. Data Cleaning

The "perfect" cleaned dataset looks like:
- only USD currency projects
- no duplicates projects
- only relevant columns
- does not contain projects ending on or after May 17, 2019 (date of last webscraped data) that have not met 100% of their goal amount 
- no missing values (very important to remove missing values AFTER removing irrelevant columns since there are columns with mostly missing values in this dataset as you can see from the .info function below)

#### Remove non-USD currency projects


```python
projects = projects[projects["currency"] == "USD"]
```

#### Remove duplicates


```python
projects = projects.drop_duplicates("project_id")

# Returns "Empty Dataframe" if there are no more duplicate rows 
print(projects[projects.duplicated("project_id")])
```

    Empty DataFrame
    Columns: [bullet_point, category, category_url, clickthrough_url, close_date, currency, funds_raised_amount, funds_raised_percent, image_url, is_indemand, is_pre_launch, offered_by, open_date, perk_goal_percentage, perks_claimed, price_offered, price_retail, product_id, product_stage, project_id, project_type, source_url, tagline, tags, title]
    Index: []
    
    [0 rows x 25 columns]
    

#### Remove irrelevant columns


```python
projects = projects.drop(["currency", "bullet_point", "project_type", "category_url", "clickthrough_url", "image_url", "is_indemand", "is_pre_launch", "offered_by", "price_retail", "source_url", "price_offered", "perks_claimed", "perk_goal_percentage", "product_stage", "product_id", "tags"], axis=1)
```

#### Remove missing values


```python
projects = projects.dropna()
```

#### Remove not-yet-successful ongoing projects

We want to look at projects that are either successful or that ended before May 17, 2019 as a failed project. 
Why? We mainly want to look at projects that ended before the scraping date May 17, 2019 because we want each project to have a fair chance of accumulating funds until their deadline. However, if a project reaches their goal funding amount before the deadline, it means that already succeeded. 


```python
# Creating new column "status" to indicate whether project is still ongoing (0) or ended (1)

projects["status"] = projects["close_date"].apply(lambda x: 1 if str(x)<"2019-05-17" else 0)

# Creating new column "success_fail" to indicate whether project succeeded (1) in getting 100% of failed (0)

projects["success_fail"] = projects["funds_raised_percent"].apply(lambda x: 1 if x>=1 else 0)
```


```python
# Creating new column "eligibility". If 1+, then it means project has either:
## ended and failed (1)
## ended and succeeded (2)
## ongoing and succeeded (1)
## DOES NOT INCLUDE projects ongoing and failed (0)

projects["eligibility"] = projects["success_fail"] + projects["status"]
projects = projects[projects["eligibility"] >= 1]
```


```python
projects
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
      <th>category</th>
      <th>close_date</th>
      <th>funds_raised_amount</th>
      <th>funds_raised_percent</th>
      <th>open_date</th>
      <th>project_id</th>
      <th>tagline</th>
      <th>title</th>
      <th>status</th>
      <th>success_fail</th>
      <th>eligibility</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>329301</th>
      <td>Art</td>
      <td>2010-06-07 06:59:00</td>
      <td>494.0</td>
      <td>0.160807</td>
      <td>2010-02-19 09:47:28</td>
      <td>3036.0</td>
      <td>Mike Hedge's Jumping Book     -a fantastic col...</td>
      <td>jumpingbook</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>102846</th>
      <td>Local Businesses</td>
      <td>2010-07-01 06:59:00</td>
      <td>4625.0</td>
      <td>1.541667</td>
      <td>2010-03-09 01:58:24</td>
      <td>3271.0</td>
      <td>DIY DAYS needs your help to make our NYC event...</td>
      <td>DIY DAYS a roving conference for those who cre...</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>23427</th>
      <td>Comics</td>
      <td>2010-11-01 06:59:00</td>
      <td>1207.0</td>
      <td>0.928462</td>
      <td>2010-04-04 05:11:06</td>
      <td>3587.0</td>
      <td>Anti-War (Non-Profit) Comic Book Anthology &amp; C...</td>
      <td>WAR The Human Cost - 260 Page Comic Book &amp; CD</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>311431</th>
      <td>Film</td>
      <td>2010-06-15 06:59:00</td>
      <td>555.0</td>
      <td>0.079286</td>
      <td>2010-04-05 18:04:16</td>
      <td>3603.0</td>
      <td>When you fall off the edge, it's not about whe...</td>
      <td>trip</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>313835</th>
      <td>Film</td>
      <td>2010-08-06 06:59:00</td>
      <td>327.0</td>
      <td>0.065400</td>
      <td>2010-04-06 18:50:21</td>
      <td>3620.0</td>
      <td>Fund the next superhero movie!</td>
      <td>Superhero Nation</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>346814</th>
      <td>Environment</td>
      <td>2010-04-26 06:59:00</td>
      <td>3620.0</td>
      <td>0.181000</td>
      <td>2010-04-16 20:50:06</td>
      <td>3838.0</td>
      <td>Sustainable Motorcycle Racing pushing Innovati...</td>
      <td>Electric Motorcycles are the Future!</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>144580</th>
      <td>Transportation</td>
      <td>2010-05-13 06:59:00</td>
      <td>840.0</td>
      <td>0.168000</td>
      <td>2010-04-22 05:38:42</td>
      <td>3936.0</td>
      <td>Pure electric motorcycle proves a powerful alt...</td>
      <td>Join the Electric Revolution!!!</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>344072</th>
      <td>Environment</td>
      <td>2010-05-17 06:59:00</td>
      <td>610.0</td>
      <td>0.061000</td>
      <td>2010-05-01 16:45:07</td>
      <td>4120.0</td>
      <td>We are very excited to race a Mavizen and we w...</td>
      <td>Be Part of History and the Electric Motorcycle...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>204277</th>
      <td>Writing &amp; Publishing</td>
      <td>2010-06-02 06:59:00</td>
      <td>565.0</td>
      <td>0.565000</td>
      <td>2010-05-04 04:59:50</td>
      <td>4177.0</td>
      <td>Why so much faith for such poor people?</td>
      <td>Faith and the Bahamian People</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>241510</th>
      <td>Health &amp; Fitness</td>
      <td>2010-05-16 06:59:00</td>
      <td>3000.0</td>
      <td>1.000000</td>
      <td>2010-05-04 06:28:41</td>
      <td>4178.0</td>
      <td>Pour, sip, unwind.</td>
      <td>The Cellar</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>307071</th>
      <td>Dance &amp; Theater</td>
      <td>2010-07-06 06:59:00</td>
      <td>4745.0</td>
      <td>0.474500</td>
      <td>2010-05-08 19:48:09</td>
      <td>4208.0</td>
      <td>There's a Terrorist in all of us.</td>
      <td>The Man Who Was Thursday</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>182409</th>
      <td>Dance &amp; Theater</td>
      <td>2010-07-02 06:59:00</td>
      <td>2610.0</td>
      <td>1.044000</td>
      <td>2010-05-06 01:44:49</td>
      <td>4216.0</td>
      <td>Donate to help make my dreams come true!</td>
      <td>Project Broadway</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>337534</th>
      <td>Writing &amp; Publishing</td>
      <td>2010-09-02 06:59:00</td>
      <td>1265.0</td>
      <td>0.253000</td>
      <td>2010-05-14 01:04:04</td>
      <td>4333.0</td>
      <td>A in-depth look at contemporary Mexico in a ye...</td>
      <td>Between Two Anniversaries</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>284351</th>
      <td>Environment</td>
      <td>2011-01-15 07:59:00</td>
      <td>447.0</td>
      <td>0.004470</td>
      <td>2010-09-15 15:19:37</td>
      <td>4543.0</td>
      <td>Knowledge is "Power"</td>
      <td>Solar House</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>306585</th>
      <td>Art</td>
      <td>2010-06-22 06:59:00</td>
      <td>510.0</td>
      <td>1.020000</td>
      <td>2010-05-22 16:19:46</td>
      <td>4560.0</td>
      <td>Limited edition art prints inspired by and cel...</td>
      <td>BYE BYE LOST</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>306014</th>
      <td>Art</td>
      <td>2010-07-22 06:59:00</td>
      <td>1755.0</td>
      <td>1.170000</td>
      <td>2010-06-08 16:52:30</td>
      <td>5024.0</td>
      <td>140 hand-painted pages of suspense-filled were...</td>
      <td>Harbor Moon - The Graphic Novel</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>326910</th>
      <td>Art</td>
      <td>2010-07-11 06:59:00</td>
      <td>520.0</td>
      <td>0.074286</td>
      <td>2010-06-09 21:38:49</td>
      <td>5044.0</td>
      <td>the art film is food for talk</td>
      <td>To the Diggers</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>84645</th>
      <td>Human Rights</td>
      <td>2010-07-03 06:59:00</td>
      <td>250.0</td>
      <td>0.208333</td>
      <td>2010-06-11 00:47:35</td>
      <td>5109.0</td>
      <td>Send Me to Haiti...I'm needed there!</td>
      <td>Relief Trip to Haiti</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>306523</th>
      <td>Art</td>
      <td>2010-08-02 06:59:00</td>
      <td>114.0</td>
      <td>0.228000</td>
      <td>2010-06-14 00:16:39</td>
      <td>5176.0</td>
      <td>COFFEE!!!!</td>
      <td>Gabe's Tentative Sweet Coffee Cup Tattoo</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>265204</th>
      <td>Environment</td>
      <td>2010-07-02 06:59:00</td>
      <td>170.0</td>
      <td>0.340000</td>
      <td>2010-06-16 07:26:26</td>
      <td>5238.0</td>
      <td>Teaching ways to self sustain and be green.</td>
      <td>Alternative Ayiti Haiti Project</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>132702</th>
      <td>Human Rights</td>
      <td>2010-07-11 06:59:00</td>
      <td>200.0</td>
      <td>0.166667</td>
      <td>2010-06-18 16:49:01</td>
      <td>5307.0</td>
      <td>Haiti Relief Mission to Leogane</td>
      <td>Out To Reach Leogane, Haiti 2010</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>10511</th>
      <td>Video Games</td>
      <td>2010-08-21 06:59:00</td>
      <td>163.0</td>
      <td>0.271667</td>
      <td>2010-06-22 01:36:24</td>
      <td>5379.0</td>
      <td>Spice Up Your Favorite RPG With Cinematica Car...</td>
      <td>Cinematica! The Blockbuster RPG Booster</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>312877</th>
      <td>Writing &amp; Publishing</td>
      <td>2010-08-29 06:59:00</td>
      <td>3000.0</td>
      <td>1.000000</td>
      <td>2010-06-30 05:22:00</td>
      <td>5612.0</td>
      <td>Bringing History to Life</td>
      <td>The Journal of Henri de Buren</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>326811</th>
      <td>Local Businesses</td>
      <td>2011-03-22 06:59:00</td>
      <td>720.0</td>
      <td>0.327273</td>
      <td>2011-01-20 20:59:22</td>
      <td>5836.0</td>
      <td>A Community Devoted to Empowering At-Risk Youth</td>
      <td>Indie-Pen-DENT! Community</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>245832</th>
      <td>Photography</td>
      <td>2010-08-22 06:59:00</td>
      <td>1360.0</td>
      <td>0.453333</td>
      <td>2010-07-10 20:13:32</td>
      <td>5912.0</td>
      <td>a photographic series of dragonflies and their...</td>
      <td>Architecture of Wings</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>324054</th>
      <td>Environment</td>
      <td>2010-09-10 06:59:00</td>
      <td>540.0</td>
      <td>0.216000</td>
      <td>2010-07-11 14:01:10</td>
      <td>5936.0</td>
      <td>enthusiastic documentary on farmer- and commun...</td>
      <td>Cultures en Transition - Voices of the Transition</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>312981</th>
      <td>Writing &amp; Publishing</td>
      <td>2010-09-28 06:59:00</td>
      <td>730.0</td>
      <td>0.365000</td>
      <td>2010-07-15 08:11:10</td>
      <td>6088.0</td>
      <td>Love isn't dead, it's just frozen.</td>
      <td>Angels of the Apocalypse</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>309722</th>
      <td>Music</td>
      <td>2010-11-19 07:59:00</td>
      <td>423.0</td>
      <td>0.084600</td>
      <td>2010-07-21 18:04:29</td>
      <td>6302.0</td>
      <td>Help fund my debut album and receive some grea...</td>
      <td>Marc Playle's Debut Album</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>305868</th>
      <td>Art</td>
      <td>2010-08-15 06:59:00</td>
      <td>195.0</td>
      <td>0.195000</td>
      <td>2010-08-04 17:43:42</td>
      <td>6794.0</td>
      <td>An Homage to Kubrick and Clarke for Burning Ma...</td>
      <td>Monotropolis</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>297278</th>
      <td>Home</td>
      <td>2010-10-08 06:59:00</td>
      <td>2194.0</td>
      <td>0.731333</td>
      <td>2010-08-16 20:43:19</td>
      <td>6866.0</td>
      <td>Fifteen 5-minute webisodes about a family who ...</td>
      <td>The Coffee Table</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>16450</th>
      <td>Film</td>
      <td>2019-05-05 06:59:59</td>
      <td>390.0</td>
      <td>0.015600</td>
      <td>2019-04-09 07:00:00</td>
      <td>2489615.0</td>
      <td>A suspense/thriller short about a man trying t...</td>
      <td>TETHER - Short Film</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9543</th>
      <td>Energy &amp; Green Tech</td>
      <td>2019-05-08 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-07 07:00:00</td>
      <td>2489704.0</td>
      <td>Students Learn Electronics - The Practical Way!</td>
      <td>Electronics Device System for Learning</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>51802</th>
      <td>Film</td>
      <td>2019-05-10 06:59:59</td>
      <td>25.0</td>
      <td>0.016667</td>
      <td>2019-04-09 07:00:00</td>
      <td>2489722.0</td>
      <td>Motion Picture Incubator class at CSULA guides...</td>
      <td>Happy Birthday Feng</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>20435</th>
      <td>Film</td>
      <td>2019-05-07 06:59:59</td>
      <td>170.0</td>
      <td>0.261538</td>
      <td>2019-04-08 07:00:00</td>
      <td>2489740.0</td>
      <td>"Guilt" is a short film spreading awareness of...</td>
      <td>Guilt: a mental illness awareness film</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>38320</th>
      <td>Music</td>
      <td>2019-05-10 06:59:59</td>
      <td>220.0</td>
      <td>0.220000</td>
      <td>2019-04-09 07:00:00</td>
      <td>2489759.0</td>
      <td>Help Bastion’s Wake secure funding to print an...</td>
      <td>Bastion’s Wake - First Album</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>31647</th>
      <td>Travel &amp; Outdoors</td>
      <td>2019-05-04 06:59:59</td>
      <td>10.0</td>
      <td>0.012579</td>
      <td>2019-04-08 07:00:00</td>
      <td>2489833.0</td>
      <td>Help young detectorist dream comes true.</td>
      <td>Metal Detecting Dream</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>33993</th>
      <td>Productivity</td>
      <td>2019-04-10 06:59:59</td>
      <td>43160.0</td>
      <td>1.716000</td>
      <td>2019-04-09 06:59:59</td>
      <td>2489875.0</td>
      <td>The world’s finest lap desk, where form and fu...</td>
      <td>Lagio: The Lap Desk of Luxury</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>8449</th>
      <td>Audio</td>
      <td>2019-05-11 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-09 07:00:00</td>
      <td>2489886.0</td>
      <td>dfsdfsafsaf</td>
      <td>Flexi Watch</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>25291</th>
      <td>Dance &amp; Theater</td>
      <td>2019-05-04 06:59:59</td>
      <td>110.0</td>
      <td>0.091667</td>
      <td>2019-04-08 07:00:00</td>
      <td>2489891.0</td>
      <td>A festival dedicated to promote Indian Classic...</td>
      <td>Contribute to help Indian Dance Festival in Vi...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>50221</th>
      <td>Video Games</td>
      <td>2019-05-12 06:59:59</td>
      <td>2.0</td>
      <td>0.000008</td>
      <td>2019-04-11 07:00:00</td>
      <td>2489893.0</td>
      <td>Lucky in next time</td>
      <td>Surge</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>18302</th>
      <td>Home</td>
      <td>2019-05-09 06:59:59</td>
      <td>10.0</td>
      <td>0.020000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2489949.0</td>
      <td>Hi guys, I am rising funds to buy my boyfriend...</td>
      <td>Buying a Pomegranate Removal Tool for my boyfr...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>Wellness</td>
      <td>2019-05-09 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2489960.0</td>
      <td>YOU can help me overcome my porn addiction!\n\...</td>
      <td>YOU can help me overcome my porn addiction!</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>54969</th>
      <td>Dance &amp; Theater</td>
      <td>2019-05-13 06:59:59</td>
      <td>40.0</td>
      <td>0.008000</td>
      <td>2019-04-10 07:00:00</td>
      <td>2489974.0</td>
      <td>Little Shop of Horrors is coming to Concord, N...</td>
      <td>Little Shop of Horrors at The Hatbox</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6019</th>
      <td>Music</td>
      <td>2019-04-20 06:59:59</td>
      <td>204.0</td>
      <td>0.340000</td>
      <td>2019-04-09 07:00:00</td>
      <td>2490012.0</td>
      <td>We are four college students looking to go on ...</td>
      <td>Help us go on tour this summer!</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>335</th>
      <td>Audio</td>
      <td>2019-04-29 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2490042.0</td>
      <td>A beautiful collection of ear cuffs with wirel...</td>
      <td>LUXE: Bluetooth enabled wearable Ear Cuffs</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12217</th>
      <td>Art</td>
      <td>2019-05-15 06:59:59</td>
      <td>7236.0</td>
      <td>0.219273</td>
      <td>2019-04-09 07:00:00</td>
      <td>2490049.0</td>
      <td>Exploring the importance of memories and ident...</td>
      <td>Anamnesis</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>17198</th>
      <td>Dance &amp; Theater</td>
      <td>2019-04-24 06:59:59</td>
      <td>270.0</td>
      <td>0.540000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2490089.0</td>
      <td>16. ODTÜ Çağdaş Dans Günleri Destek Çağrısı</td>
      <td>16. ODTÜ Çağdaş Dans Günleri</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>49144</th>
      <td>Fashion &amp; Wearables</td>
      <td>2019-05-09 06:59:59</td>
      <td>30.0</td>
      <td>0.003000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2490136.0</td>
      <td>The gift of giving starts with you.</td>
      <td>Woman Owned Play it Forward Gift Boutique</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>36931</th>
      <td>Photography</td>
      <td>2019-05-10 06:59:59</td>
      <td>198.0</td>
      <td>0.008939</td>
      <td>2019-04-08 07:00:00</td>
      <td>2490147.0</td>
      <td>Patented artworks project financial assistance...</td>
      <td>Aldolphus Artworks Patent Supports Art Genres</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6281</th>
      <td>Film</td>
      <td>2019-05-11 06:59:59</td>
      <td>203.0</td>
      <td>0.203000</td>
      <td>2019-04-08 07:00:00</td>
      <td>2490167.0</td>
      <td>'DEPARTMENT 666' - an original psychological h...</td>
      <td>Department 666</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>16285</th>
      <td>Productivity</td>
      <td>2019-05-10 06:59:59</td>
      <td>41.0</td>
      <td>0.002733</td>
      <td>2019-04-10 07:00:00</td>
      <td>2490241.0</td>
      <td>Enhance work life the natural way. No books , ...</td>
      <td>Mybeat: 1st smart music engine to be great at ...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>25586</th>
      <td>Podcasts, Blogs &amp; Vlogs</td>
      <td>2019-05-10 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-09 07:00:00</td>
      <td>2490549.0</td>
      <td>Creating quality content that brings the horro...</td>
      <td>Just-Horror Blog and Reviews</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>34498</th>
      <td>Film</td>
      <td>2019-05-10 06:59:59</td>
      <td>500.0</td>
      <td>0.071429</td>
      <td>2019-04-09 07:00:00</td>
      <td>2490561.0</td>
      <td>Calamity is an upcoming short film being direc...</td>
      <td>Calamity SHORT FILM</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>7914</th>
      <td>Comics</td>
      <td>2019-05-05 06:59:59</td>
      <td>165.0</td>
      <td>0.330000</td>
      <td>2019-04-09 07:00:00</td>
      <td>2490618.0</td>
      <td>A 75 page hardcover book of beautiful illustra...</td>
      <td>Uncut Comic Artbook 'Special Edition'</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>29941</th>
      <td>Film</td>
      <td>2019-04-25 06:59:59</td>
      <td>360.0</td>
      <td>0.480000</td>
      <td>2019-04-10 07:00:00</td>
      <td>2490724.0</td>
      <td>"Awkward" by Jared Wayne &amp; Killian Ryan; follo...</td>
      <td>Awkward</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>25220</th>
      <td>Film</td>
      <td>2019-05-11 06:59:59</td>
      <td>770.0</td>
      <td>0.077000</td>
      <td>2019-04-10 07:00:00</td>
      <td>2490864.0</td>
      <td>Tina Makharadze reinvents the language of deat...</td>
      <td>MAMA</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>38585</th>
      <td>Music</td>
      <td>2019-04-10 06:59:59</td>
      <td>16727.0</td>
      <td>1.018232</td>
      <td>2019-04-09 06:59:59</td>
      <td>2490888.0</td>
      <td>The hidden harmonies of your favourite Bach Ce...</td>
      <td>B2C: Bach Cello Suites UNLEASHED!</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
    </tr>
    <tr>
      <th>28445</th>
      <td>Photography</td>
      <td>2019-04-25 06:59:59</td>
      <td>350.0</td>
      <td>0.140000</td>
      <td>2019-04-10 07:00:00</td>
      <td>2490894.0</td>
      <td>Help me go on a African Photo Safari!</td>
      <td>African Photo Safari</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>17594</th>
      <td>Tabletop Games</td>
      <td>2019-04-17 06:59:59</td>
      <td>29.0</td>
      <td>0.058000</td>
      <td>2019-04-11 07:00:00</td>
      <td>2491130.0</td>
      <td>Playing Card Sketch Pad is BACK by popular dem...</td>
      <td>Playing Card Sketch Pad is BACK by popular dem...</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>47785</th>
      <td>Wellness</td>
      <td>2019-05-02 06:59:59</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>2019-04-11 07:00:00</td>
      <td>2491142.0</td>
      <td>I am at risk of getting suspended from school ...</td>
      <td>School fee</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
<p>44129 rows × 11 columns</p>
</div>




```python
projects.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 44129 entries, 329301 to 47785
    Data columns (total 11 columns):
    category                44129 non-null object
    close_date              44129 non-null datetime64[ns]
    funds_raised_amount     44129 non-null float64
    funds_raised_percent    44129 non-null float64
    open_date               44129 non-null datetime64[ns]
    project_id              44129 non-null float64
    tagline                 44129 non-null object
    title                   44129 non-null object
    status                  44129 non-null int64
    success_fail            44129 non-null int64
    eligibility             44129 non-null int64
    dtypes: datetime64[ns](2), float64(3), int64(3), object(3)
    memory usage: 4.0+ MB
    


```python
projects['category'].unique()
```




    array(['Art', 'Local Businesses', 'Comics', 'Film', 'Environment',
           'Transportation', 'Writing & Publishing', 'Health & Fitness',
           'Dance & Theater', 'Human Rights', 'Video Games', 'Photography',
           'Music', 'Home', 'Productivity', 'Fashion & Wearables',
           'Phones & Accessories', 'Education', 'Camera Gear',
           'Animal Rights', 'Travel & Outdoors', 'Wellness',
           'Food & Beverages', 'Web Series & TV Shows', 'Spirituality',
           'Audio', 'Tabletop Games', 'Energy & Green Tech',
           'Podcasts, Blogs & Vlogs', 'Culture'], dtype=object)


