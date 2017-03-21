<h2> About me </h2>
<p> Hello! My name is Ruoxin Li, you can also call me Rita if you want. I am a second year master's student in the department of Statistics at UC Davis. This is my first personal webpage for data analysis projects.My github repository is https://github.com/RuoxinLi . </p>





<h2 align = "center"> My 141B project ----Data Analysis and Visualization </h2>
<h3> motivation </h3>
 <p>As we all know, America is a country full of diversive culture. People from all over the  world come here to work,study and live. Among different elements, food is one of the most interesting topics we want to discover since it is so deeply involved in our daily life. On a large scale, we are interested in the spatial distribution of number of restaurants in different states. Meanwhile, understanding restaurant’s price, rating, number of reviews and their kinds’ distribution will give us a rough idea about our own choice of food. As we go further in the discovery of restaurants, we expect we can find out connection between them by their kinds.</p>
 <p> All those questions drive us to Yelp, a popular website displaying restaurants information. The source of our data is Yelp’s API and its website and both our analysis and visualization are displayed in the following sections.<p>

<h3> Dataset <h3>
<ol>
<li>
<h5>Web scraping
</h5>
<p>At first, we try to use yelp api. Unfortunately, Yelp api only returns 20 maximum restaurant records each time we call a search, which is far from enough for analysis. To get enough amount of data, we then turn to scrap the website page by page as an alternative choice.</p>
<p>
The total number of pages we scrap is over 20000. During the search process, we found out that using “City” as search term is not a wise choice. Since the upper limit of the number of records displayed for any search term  is 1000. It largely limited the amount of data we can get for analysis. Our strategy to solve this problem is to split one city into multiple sub-areas, for instance detroit is splitted into Downtown Detroit, Detroit Riverside etc. In small areas, the number of restaurants will not exceed the upper bound for search records. Then we can get almost all restaurants in a city by simply adding up all the records we get from scraping each small area. There is also downside of this method, we can not using such method to scrap large cities like NYC, since there is no guarantee that the number of records in even small area will be less than 1000.
</p>
</li>

<li>
<h5>Data Processing</h5>
<p>After getting the data from webpage, we first reformat some of the results. For instance, we treat reviews as numeric value and price as factors. Also we ignore the records with missing value. Finally we stack all those variables we got to be a dataframe.</p>
<p>
For the purpose of convenience, we group our raw tag set into 9 new categories. The original tags and our grouping methods are listed below. 
</p>

</li>
</ol>
```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```



<h3> More projects </h3>
Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/RuoxinLi/RuoxinLi.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
