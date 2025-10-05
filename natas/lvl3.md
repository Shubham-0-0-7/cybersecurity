## Natas 3

### Theory
Today’s internet is indexed by search-engine crawlers, such that Google and Co know what content exists on websites to improve search engine results. The robots.txt file exists on servers to tell these crawlers and other web bots, which part of the website can be visited. It allows defining a user-agent aka for what specific bot the rules should be, and which page of the website the user-agent is not allowed to visit.  
```bash
User-agent: Googlebot
Disallow: /nogooglebot/

User-agent: *
Allow: /

Sitemap: https://www.example.com/sitemap.xml
```

Here, for all crawlers/bots the whole website can be visited, except Google’s crawler, which cannot visit the ’nogooglebot’ directory. Also noticeable is the sitemap link. A sitemap is another file to give web crawlers information about the website.
It is important to be aware that the ‘robots.txt’ file does NOT serve a security purpose. The disallowed pages can still be visited and might still show up in search engines. Read more about this file in the (Google Developer Docs)[https://developers.google.com/search/docs/crawling-indexing/robots/create-robots-txt].   


### Solution

Opening the website and looking at the source code, we can find a comment, which gives us a hint: <!-- No more information leaks!! Not even Google will find it this time... -->.

This sound like the robots.txt file, I explained in the theory section. Navigating to the site: http://natas3.natas.labs.overthewire.org/robots.txt reveals that the file exists and it shows a disallowed path. Navigating to this path, again leads to a public directory with a ‘user.txt’ file, which contains the password to the next level.


