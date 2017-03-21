<h2> About me </h2>

<img src="phot.jpg" width = "100px" height = "100px"/>
   

<p> Hello! My name is Ruoxin Li, you can also call me Rita if you want. I am a second year master's student in the department of Statistics at UC Davis. This is my first personal webpage for data analysis projects.My github repository is <a>https://github.com/RuoxinLi</a> . </p>





<h2 align = "center"> My 141B project ----<a href = "https://github.com/miaoelenawang/STA-141B-Final-Project/tree/master/">Data Analysis and Visualization for Yelp</a> </h2>
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

<h3 align = "center">Interesting Insights</h3>

<p> Firstly we discovered single variables. Rating and price are important attributes of restaurants. In the dataset, ratings are real numbers from 0 to 5 while price are categorical(1: Inexpensive, 2: Moderate, 3: Pricey, 4: Highend). We are interested in Price and Rating's distribution in different cities and restaurants tags.

<h4>Taste in different cities</h4>
<ol>
        <li>In general, cities affect the distribution of rating for some kinds of food. 
                    For example, Albuquerque has a larger proportion of Low ratings for European food 
                    than other cities.</li>
                <li>In San Francisco, Alcohol, Dessert, American and South American(Mexican) food tend to 
                have higher ratings than other kinds of food.</li>
                <li>In Albuquerque, Dessert, American and South American(Mexican) food have a larger percentage of high ratings,
                    while American food has a larger proportion of low ratings than others.</li>
                <li>In Detroit, Dessert and Southeast Asian food have a larger percentage of high ratings.</li>
                <li>In conclusion, people in different areas might have different tastes. </li>
            </ol> 
<h4>Spatial Difference of Price and Rating in three cities</h4>
       
<ol>
          <li><strong> Rating </strong>
                    <ul>
                          <li>In San Francisco, restaurants in Bayview-Hunters Point, Bernal Heights, Castro, Misson, Potrero Hill,
                          SoMa areas have higher rating than others.</li>
                          <li>In Albuquerque, restaurants in Barelas/South Valley, Downtown and International District areas have
                          higher rating than others.</li>
                          <li>In Detroit, restaurants in Eastern Market, Southwest Detroit, Warrendale areas have higher rating than
                          others.</li>
                    </ul>
                </li>
        </ol>
	   <ol>
                <li><strong> Price </strong>
                    <ul>
                          <li>For each city, price of restaurants in different areas are concentrated on "$" and "$$".
                              But there still exists slightly difference.</li>
                          <li>In San Francisco, restaurants in Chinatown, Inner Sunset, Mission, Outer Sunset, Tenderlion areas 
                              are cheaper than others.</li>
                          <li>In Albuquerque, restaurants in the Barelas/South Valley area are cheaper than others.</li>
                          <li>In Detroit, restaurants in Downtown Birmingham and Downtown Royal Oak areas are little more expensive than
                          others.</li>
                    </ul>
                </li>
  </ol>
  <p> A more detailed analysis is on <a href = "https://github.com/miaoelenawang/STA-141B-Final-Project/tree/master">project repository</a></p>
  
 <h4>Spatial Grouping in network of restaurants in two cities</h4>
 <h4>Introduction</h4>
  <p>From the Yelp website, we find the relatedness of each restaurant. It is known that Yelp recommends those related restaurant for similarity between them. For instance, restaurants may sell same kinds of food or are close to each other. Therefore we want to recover such similarity between those restaurants which are related. In the network graph, each node is a restaurant and edges are connections. Here we color the nodes by their tags( after cleaning). We are hoping we can see some grouping on the nodes of the network graph.
 <br> Note: Since it is possible for each restaurant to have multiple tags, we add boolean attributes to each restaurant( such as whether a restaurant has tag "Chinese" or not). Therefore, if we click on tag in the legend, the colored nodes are restaurants with that tag, while the gray one are restaurants does not have that tag.</p>
  
  <div>
    <div>Analysis for Detroit</div>
          <div >
		  <div>
    <a href="https://plot.ly/~Rita0309/36/?share_key=zmEfWFVDn5NlCo00UkJX1f" target="_blank" title="Detroit.html" style="display: block; text-align: center;"><img src="https://plot.ly/~Rita0309/36.png?share_key=zmEfWFVDn5NlCo00UkJX1f" alt="Detroit.html" style="max-width: 100%;width: 1000px;"  width="1000" onerror="this.onerror=null;this.src='https://plot.ly/404.png';" /></a>
    <script data-plotly="Rita0309:36" sharekey-plotly="zmEfWFVDn5NlCo00UkJX1f" src="https://plot.ly/embed.js" async></script>
</div>

                   
 
		   <h3> Chinese</h3>
		   <p>Chinese restaurant are grouped well. And also the number of Chinese restaurant is very small in Detroit.(click all the trace except chinese)</p>
		   <h3>Alcohol</h3>
		   <p> Though bars and pubs are not well grouped but most of them are still connected.</p>
		   <h3>American</h3>
		   <p>Most american restaurant are spanned on connected nodes.And the number of american food restaurant is the most</p>
		   <h3> South American</h3>
		   <p>Most South american restaurant are spanned on connected nodes</p>
		   <h3> Desert, Europe Food</h3>
		   <p>Restaurant in those tags are not well grouped. Since there are other factors like location also involved in their connection</p>
		   <p>From the network, we can also get a rough idea about how the restraurant distributed in each tag. Apparently the number of restaurant under that tags are: American > Europe, Alcohol, Dessert> South American > Chinese > Other in Detroit.

           <br>And also we noticed that the connection are not only formed by tags, some of the restaurant is connected since they are in the same place. Therefore to better cluster them, we can adding the "area“ as variable.</p>
		   
		   
		   
		   
		  </div>
    </div>


<h3> More projects </h3>
(https://github.com/RuoxinLi/RuoxinLi.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

