# Overview #
GAELucene is a lucene component that can help you to run search applications on Google AppEngine.

# Developer's Guide #
The main clazz of GAELucene include:

  * GAEDirectory - a read only Directory based on google datastore.
  * GAEFile - stands for an index file, the file's byte content will be splited into multi GAEFileContent.
  * GAEFileContent - stands for a segment of index file.
  * GAECategory - the identifier of different indices.
  * GAEIndexInput - a memory-resident IndexInput implementation like the RAMInputStream.

  * GAEIndexReader - wrapper for IndexReader that cached in GAEIndexReaderPool
  * GAEIndexReaderPool - pool for GAEIndexReader

The following code snippet demonstrates the use of GAELucene do searching:

```
Query queryObject = parserQuery(request);
GAEIndexReaderPool readerPool = GAEIndexReaderPool.getInstance();
GAEIndexReader indexReader = readerPool.borrowReader(INDEX_CATEGORY_DEMO);
IndexSearcher searcher = new IndexSearcher(indexReader);
Hits hits = searcher.search(queryObject);
readerPool.returnReader(indexReader);
```

# Notice #
The GAEDirectory is read only, that is, you can not use the Directory to build index! You should do indexing on another machine, then push the indices onto google appengine datastore with LuceneIndexPushUtil.

Because of the quota limitation of google appengine, GAELucene is not fit to run with huge indices, it does better for small indices, around 100Mb. For large changing indices, you need to find other solutions.

# Quick Start #

## Run GAELucene in eclipse ##

Suppose that you have integrated the google appengine sdk and svn client within your eclipse IDE.

1. Checkout GAELucene from the svn repository http://gaelucene.googlecode.com/svn/trunk/

2. Define reserved user in '${GAELUCENE\_HOME}/war/WEB-INF/classes/gaelucene-users.txt'
> Notice: A google account's email each line, line starts with '#' will be omitted. The reserved user will act as 'System Administrator'.

3. Download and untar the prepackaged indices tar file[(10 pices from lucen mail list)](http://gaelucene.googlecode.com/files/demoindices.tar) into '${GAELUCENE\_HOME}/war/WEB-INF/classes/'
> Notice: Ensure the untared index files were under '${GAELUCENE\_HOME}/war/WEB-INF/classes/indices/demo/index/'

4. Compile and run your google web application
> Visit the dashboard http://localhost:8080/gaelucenedashboard/index. You may be request to log in with your reserved user account.

5. Import the packaged indices onto google datastore
> From the dashboard index page, visit 'Import Indices' http://localhost:8080/gaelucenedashboard/importpackagedindex?dir=indices/demo/index&cat=demo

6. Test the Demo page
> Visit http://localhost:8080/demo.jsp

## Deploy GAELucene onto google appengine ##

## Push fresh index onto google datastore ##

> Use LuceneIndexPushUtil to interact with your deploied GAELucene webapp.
```
  $ java org.apache.gaelucene.tools.LuceneIndexPushUtil 
    -app-url ${gaeAppURL}
    -auth-cookie ${authCookie}
    -src ${path-to-index-folder}
    -cat ${index-category}
    -rec-file ${path-to-rec-file}
```
```
  $ java -Xms128m -Xmx256m org.apache.gaelucene.tools.LuceneIndexPushUtil
    -app-url "http://gaelucene.appspot.com/gaelucenedashboard"
    -auth-cookie "ahlogincookie=gaelucene@gmail.com:false:12093021971246851751"
    -src "${DATA}/demo/index"
    -cat "demo"
    -rec-file "${DATA}/demo/uploaded.rec"
```

# Case #
Here, [3gmatrix-search.appspot.com](http://3gmatrix-search.appspot.com), is a site based on GAELucene.


# Learned from #

[mvnForum](http://www.mvnforum.com/) - The Permission/Authentication framework