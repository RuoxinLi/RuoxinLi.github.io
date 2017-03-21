<h2> About me </h2>
<div>
   <table width="25%" height="25%" align="center" valign="center">
   <tr><td>
      <img src="phot.jpg" />
   </td></tr>
   </table>
</div>

<p> Hello! My name is Ruoxin Li, you can also call me Rita if you want. I am a second year master's student in the department of Statistics at UC Davis. This is my first personal webpage for data analysis projects.My github repository is <a>https://github.com/RuoxinLi</a> . </p>





<h2 align = "center"> My 141B project ----Data Analysis and Visualization </h2>
<h3> Questions </h3>
 <p>Food is one of the most interesting topics we want to discover since it is so deeply involved in our daily life. On a large scale, we are interested in the spatial distribution of number of restaurants in different states. We want to answer the following questions:
how do price, rating, number of reviews are related to each other? How do price and rating varies across different cities? Do different kinds of restaurant has similar price or rating? As we go further in the discovery of restaurants, we also noticed that Yelp's recommendation systerm recommend restaurants by high similarity and we want to know all same kinds of restaurants always being connected(recommended)?</p>
 <p> All those questions drive us to Yelp, a popular website displaying restaurants information. The source of our data is Yelp’s API and its website and both our analysis and visualization are displayed in the following sections.<p>

<h3> Dataset </h3>


<h5>Web Scraping Strategy
</h5>
<p>
The total number of pages we scrap is over 20000. During the search process, we found out that using “City” as search term is not a wise choice. Since the upper limit of the number of records displayed for any search term  is 1000. It largely limited the amount of data we can get for analysis. Our strategy to solve this problem is to split one city into multiple sub-areas, for instance detroit is splitted into Downtown Detroit, Detroit Riverside etc. In small areas, the number of restaurants will not exceed the upper bound for search records. Then we can get almost all restaurants in a city by simply adding up all the records we get from scraping each small area. There is also downside of this method, we can not using such method to scrap large cities like NYC, since there is no guarantee that the number of records in even small area will be less than 1000.
</p>
<p>Here is one example we scraped single page. For more details, please visit our <a href = "https://github.com/miaoelenawang/STA-141B-Final-Project/tree/master/web_scraping">project repository</a></p>


<pre><code class="python">


def extract_single_page(url):
	#url = "https://www.yelp.com/biz/craftsman-and-wolves-the-den-san-francisco-2"
	page = requests.get(url)
	tree = lx.fromstring(page.content)
	try:
		claim = tree.xpath('//div[@class="u-nowrap claim-status_teaser js-claim-status-hover"]')[0].text_content()
		claims = re.sub(" ","",claim).strip('\n')
	except:
		claims = None
	try:
		health_inspect = tree.xpath('//dd[@class="nowrap health-score-description"]')[0].text_content()
		hl_inspect = re.sub(" ","",health_inspect).strip('\n')
	except:
		hl_inspect = None

	week = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun']
	hours = {i : None for i in week}
	try:
		hour = tree.xpath('//table[@class="table table-simple hours-table"]//tr')
		hou = {i.xpath('th')[0].text_content().strip():i.xpath('td')[0].text_content().strip() for i in hour}			
		for wkday in week:		
			hours.update({wkday:hou[wkday]})
	except:
		hours = hours	

	try:
		more_info= tree.xpath('//div[@class="short-def-list"]')[0].text_content()
		more_info= [ii for ii in re.sub(" ","",more_info).split('\n') if ii not in ['']]
		more_info = {more_info[i]:more_info[i+1] for i in range(0,len(more_info)-1,2)}

	except:
		more_info = None
	try: 
		loc = tree.find_class('lightbox-map hidden')[0].get('data-map-state')
		d = json.loads(loc)
		latitude = d.get('center').get('latitude')
		longitude = d.get('center').get('longitude')
	except:
		latitude = None
		longitude = None

	li = tree.xpath("//div/h3[text()='People also viewed']/../ul/li")
	related3 = {'related'+str(i):None for i in range(3)}
	try:
		related = [i.find_class('js-analytics-click')[0].get('href').split('/')[2].split('?')[0] for i in li]
		k = 0
		for j in related:
			related3.update({'related'+str(k):j}) 
			k +=1
	except:    
		related3 = {'related'+str(i):None for i in range(3)}

	Business = {"claimed status":claims,"health inspect":hl_inspect,"more information":more_info, 'latitude':latitude,'longitude':longitude}
	Business.update(hours)  
	Business.update(related3)  
	return Business


</code></pre>


<h5>Data Processing</h5>
<p>After getting the data from webpage, we first reformat some of the results. For instance, we treat reviews as numeric value and price as factors. Also we ignore the records with missing value. Finally we stack all those variables we got to be a dataframe.</p>
<p>
For the purpose of convenience, we also group our raw tag set into 9 new categories. The original tags and our grouping methods are listed below. A more detailed explanation on how we deal with the dataset is on our project webpage. 
</p>

<h3> align = "center">Question we answered</h3>
<h4> Distribution of Price, Rating in different categories.<h4>
<p> Firstly we discovered single variables. Rating and price are important attributes of restaurants. In the dataset, ratings are real numbers from 0 to 5 while price are categorical(1: Inexpensive, 2: Moderate, 3: Pricey, 4: Highend). We are interested in Price and Rating's distribution in different cities and restaurants tags. 







<h3> More projects </h3>
(https://github.com/RuoxinLi/RuoxinLi.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

