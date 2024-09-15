---
layout: post
title: "Web Scraping with Cheerio and Puppeteer: A Comprehensive Guide"
date: 2024-09-16 00:22:00 +0530
categories: mardown html tool web
---

Web scraping is a powerful technique used to extract data from websites. It involves fetching web pages and extracting relevant information from them, which can be used for analysis, monitoring, or other purposes. In this guide, we’ll explore how to scrape data using two popular tools: Cheerio and Puppeteer. We’ll also discuss how to store and manage this data using GitHub repositories, automate the process, and provide some interesting data scraping ideas.

## Understanding Web Scraping

Web scraping involves several key steps:

1. **Fetching the Web Page**: This is the process of retrieving the HTML content of a web page. This can be done using HTTP requests to the website’s server.

2. **Parsing the HTML**: Once the HTML content is retrieved, it needs to be parsed to extract the information you’re interested in. This is where tools like Cheerio and Puppeteer come into play.

3. **Extracting Data**: After parsing, you can use selectors to target specific elements in the HTML and extract the data you need.

4. **Storing Data**: Once you’ve extracted the data, it needs to be stored in a format that’s easy to manage and analyze.

## Web Scraping with Cheerio and Puppeteer

### Cheerio

Cheerio is a lightweight and fast library that makes it easy to parse and manipulate HTML in Node.js. It provides a jQuery-like syntax to select and extract data from HTML documents.

Here’s a basic example of how to use Cheerio to scrape quotes:

```javascript
const axios = require("axios");
const cheerio = require("cheerio");
const fs = require("fs");

const URL = "https://www.nitch.com/"; // Replace with the actual URL

const fetchQuotes = async (url) => {
  try {
    // Fetch the HTML of the page
    const { data } = await axios.get(url);
    const $ = cheerio.load(data);

    // Array to store quotes
    const quotes = [];

    // Extract quotes and other data
    $("article.post").each((index, element) => {
      const quoteText = $(element).find("p").text().trim();
      const image = $(element).find("img").attr("src");
      const author = quoteText.split("//")[0].trim();
      const quote = quoteText.split("//")[1]?.trim() || "";

      quotes.push({ author, quote, image });
    });

    console.log("Fetched quotes from:", url);
    console.log("Quotes:", quotes);

    // Save quotes to quotes.json
    fs.appendFileSync("quotes.json", JSON.stringify(quotes, null, 2) + ",\n");

    // Find the "Next" link
    const nextLink = $("footer nav a#next").attr("href");

    if (nextLink) {
      const nextURL = new URL(nextLink, baseURL).toString();
      console.log("Next URL:", nextURL);

      // Fetch the next page
      await fetchQuotes(nextURL);
    } else {
      console.log("No more pages.");
    }
  } catch (error) {
    console.error("Error fetching quotes:", error);
  }
};

fetchQuotes(URL);
```

### Puppeteer

Puppeteer is a Node library that provides a high-level API to control Chrome or Chromium over the DevTools Protocol. It’s useful for scraping data from websites that require JavaScript to render content.

Here’s a basic example of how to use Puppeteer to scrape quotes:

```javascript
const puppeteer = require("puppeteer");
const fs = require("fs");

const URL = "https://www.nitch.com/"; // Replace with the actual URL

const fetchQuotes = async (url) => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto(url);

  // Extract quotes and other data
  const quotes = await page.evaluate(() => {
    const quotesArray = [];
    document.querySelectorAll("article.post").forEach((element) => {
      const quoteText = element.querySelector("p").textContent.trim();
      const image = element.querySelector("img").src;
      const author = quoteText.split("//")[0].trim();
      const quote = quoteText.split("//")[1]?.trim() || "";

      quotesArray.push({ author, quote, image });
    });
    return quotesArray;
  });

  console.log("Fetched quotes from:", url);
  console.log("Quotes:", quotes);

  // Save quotes to quotes.json
  fs.appendFileSync("quotes.json", JSON.stringify(quotes, null, 2) + ",\n");

  // Find the "Next" link and navigate to it
  const nextURL = await page.evaluate(() => {
    const link = document.querySelector("footer nav a#next");
    return link ? link.href : null;
  });

  if (nextURL) {
    console.log("Next URL:", nextURL);
    await fetchQuotes(nextURL);
  } else {
    console.log("No more pages.");
  }

  await browser.close();
};

fetchQuotes(URL);
```

## Storing and Managing Data in GitHub

GitHub is an excellent platform for storing and managing your scraped data. Here’s how you can set up a GitHub repository for your data and automate updates:

### Setting Up Your GitHub Repository

1. **Create a New Repository**:

   - Go to [GitHub](https://github.com/) and log in to your account.
   - Click on the “+” icon and select “New repository.”
   - Name your repository (e.g., `data`) and add a description.
   - Choose the visibility of your repository (Public or Private).
   - Click on “Create repository.”

2. **Add Your Data to the Repository**:

   - On your local machine, navigate to the folder with your scraped data.
   - Initialize a Git repository in this folder:

     ```bash
     git init
     ```

   - Add your scraped data files:

     ```bash
     git add quotes.json
     ```

   - Commit the files:

     ```bash
     git commit -m "Add initial scraped data"
     ```

   - Link your local repository to GitHub:

     ```bash
     git remote add origin https://github.com/yourusername/data.git
     ```

   - Push your changes:

     ```bash
     git push -u origin master
     ```

### Automating Data Updates

To keep your GitHub repository updated with the latest data, automate the process using Node.js and `execSync` to run commands:

1. **Create an Automation Script**:

   ```javascript
   const { execSync } = require("child_process");

   // Run your scraping script
   execSync("node scrape.js", { stdio: "inherit" });

   // Add changes to Git
   execSync("git add quotes.json", { stdio: "inherit" });

   // Commit changes
   execSync('git commit -m "Update scraped data"', { stdio: "inherit" });

   // Push changes to GitHub
   execSync("git push", { stdio: "inherit" });
   ```

2. **Schedule Regular Updates**:

   - **Cron (Unix-like systems)**: Add a cron job to run the script daily:

     ```bash
     0 0 * * * /usr/bin/node /path/to/updateData.js
     ```

   - **Task Scheduler (Windows)**: Create a new task to run the script at your desired frequency.

## Data Scraping Ideas

Here are ten interesting types of data you can scrape and store in your GitHub repository:

1. **Quotes**: Collect motivational or famous quotes along with the authors.

2. **Product Prices**: Track prices of products from e-commerce websites.

3. **Weather Data**: Collect weather information for different locations.

4. **Job Listings**: Scrape job postings from various websites.

5. **News Articles**: Gather headlines and content from news websites.

6. **Real Estate Listings**: Collect information about properties for sale or rent.

7. **Restaurant Menus**: Scrape menu items and prices from restaurant websites.

8. **Book Information**: Track book details such as titles, authors, and reviews.

9. **Social Media Posts**: Analyze posts, hashtags, or user data from social media platforms.

10. **Event Listings**: Gather information about upcoming events, concerts, or conferences.

## Conclusion

Web scraping is a powerful technique for extracting data from websites, and tools like Cheerio and Puppeteer make this process efficient and straightforward. By storing your scraped data in GitHub, you can leverage version control, collaboration, and accessibility benefits. Automating the update process ensures your data remains current, while GitHub provides a robust platform for managing and sharing your data. With the right setup and a bit of creativity, you can scrape and manage a wide range of valuable data.
