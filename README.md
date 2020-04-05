# gatsby-source-multigitmarkdown

## Description

The plugin is a source plugin that helps in pulling content of MD files from different github repositories. 
It can also be configured to get content from same repository but different md files. 
Each md file content and it's configured metadata is created as a node in an array of nodes. 


### Dependencies

The project used the following libraries 
 - axios (for making the http call to fetch content)
 - remark (for parsing the contents of md files into a HTML)


## How to install

To install the plugin you may run the following command

For NPM: 
```
   npm -i gatsby-source-multigitmarkdown
```

For Yarn:
```
    yarn add gatsby-source-multigitmarkdown
```

To use the plugin, add the following into the `gatsby-config.js`

```js
module.exports = {
  siteMetadata: siteMetadata,
  plugins: [
    {
      resolve: `gatsby-multigitsource-plugin`,
      options: {
        pages: [{
          repo: "arunmadhavan-g/config-driven-ui",
          file: "README.md",
          title: "Building a Config Driven UI in React - Part 1",
          publishedOn: "2019-11-24T12:39:18.088Z",
          author: "Arun Madhavan",
          tags: ["reactjs", "javascript", "ui"],
          description: "Part 1 of a 2 part blog, where I share my experiences with development of a configuration driven UI to build their executive dashboard",
        }]
      }
    }]
}
```

## Available options

| Options | Description |
| --- | --- |
|pages.repo| the username and repository name part of the repo URL |
|pages.file | The name of the md file |
|pages.title | The Page title |
|pages.publishedOn | The published date of the page |
|page.author | author's name |
|page.tags | tags associated with the page |
|page.description | A short Description of what the page does |
 
 The above mentioned options would be exposed as part of the graphql node. 
 Information passed in pages.repo and pages.file will be utlilised for fetching the md file content.
 
 

## When do I use this plugin?

The motivation behind building this plugin was to aggregate content from multiple github repositries into a single blog platform
This helps in adding pages with sources coming from different public repositories md file into a single place. 


## Examples of usage

Add the following code with proper options to your `gatsby-config.js`
```js
module.exports = {
  siteMetadata: siteMetadata,
  plugins: [
    {
      resolve: `gatsby-multigitsource-plugin`,
      options: {
        pages: [{
          repo: "arunmadhavan-g/config-driven-ui",
          file: "README.md",
          title: "Building a Config Driven UI in React - Part 1",
          publishedOn: "2019-11-24T12:39:18.088Z",
          author: "Arun Madhavan",
          tags: ["reactjs", "javascript", "ui"],
          description: "Part 1 of a 2 part blog, where I share my experiences with development of a configuration driven UI to build their executive dashboard",
        }]
      }
    }]
}
```

The data can be queried using the following graphQL query
```js
query MyQuery {
  allMultiGitSource {
    edges {
      node {
        pageInfo {
          author
          content
          description
          pagePath
          publishedOn
          tags
          title
        }
      }
    }
  }
}
```

For example we can query the data in `gatsby-node.js` and createPages for each of the returned information as shown below

```js
exports.createPages = async ({ graphql, actions: { createPage } }) =>
  (await graphql(`
  query MyQuery {
    allMultiGitSource {
      edges {
        node {
          pageInfo {
            author
            content
            description
            pagePath
            publishedOn
            tags
            title
          }
        }
      }
    }
  }`)).data.allMultiGitSource.edges.map(x => x.node.pageInfo).forEach(page => {
    createPage({
      path: `/${_.camelCase(page.title)}`,
      component: require.resolve("./src/templates/page-template.js"),
      context: { page },
    })
  })
```

## How to query for data

The data is made available as part of `allMultiGitSource` node. 
Contents can be queried using a query like below. 

```js
query MyQuery {
  allMultiGitSource {
    edges {
      node {
        pageInfo {
          author
          content
          description
          pagePath
          publishedOn
          tags
          title
        }
      }
    }
  }
}
``` 
