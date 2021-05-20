# SC5.5.4
Assignment: mec-5.5.4-web-scraping-mini-project

scripts:

user@ubuntu:~/SC5.5.4/scrapyminiproject/scrapyminiproject$ more spiders/toscrape-css.py  
```python  
import scrapy  

class QuotesSpider(scrapy.Spider):
    name = "toscrape-css"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
    ]  
    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }
        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            next_page = response.urljoin(next_page)
            yield scrapy.Request(next_page, callback=self.parse)
```

user@ubuntu:~/SC5.5.4/scrapyminiproject/scrapyminiproject$ more spiders/toscrape-xpath.py  
```python  
import scrapy
class QuotesSpider(scrapy.Spider):
    name = "toscrape-xpath"
    start_urls = [
        'http://quotes.toscrape.com/page/1/',
    ]

    def parse(self, response):
        for index, quote in enumerate(response.xpath('//div[contains(@class, "quote")]')):
            yield {
                'text': quote.xpath('span/text()').extract_first(),
                'author': quote.xpath('span//small/text()').extract_first(),
                'tags': quote.xpath('div[contains(@class, "tags")]//a/text()').extract()
            }
        next_page = response.xpath('//li[contains(@class, "next")]//a/@href').extract_first()
        if next_page is not None:
            next_page = response.urljoin(next_page)
            yield scrapy.Request(next_page, callback=self.parse)
```  

run:  
`scrapy crawl toscrape-css -o css-scraper-results.json`  
`scrapy crawl toscrape-xpath -o xpath-scraper-results.json`  

outputs:  
  https://github.com/wdmhouston/SC5.5.4/blob/main/scrapyminiproject/scrapyminiproject/css-scraper-results.json  
  https://github.com/wdmhouston/SC5.5.4/blob/main/scrapyminiproject/scrapyminiproject/xpath-scraper-results.json  
