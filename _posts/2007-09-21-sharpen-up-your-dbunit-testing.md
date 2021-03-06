---
layout: post
header-img: img/default-blog-pic.jpg
author: saket_vishal
description: 
post_id: 288
created: 2007/09/21 11:08:04
created_gmt: 2007/09/21 09:08:04
comment_status: open
---

# Sharpen up your DbUnit testing

DbUnit is a JUnit extension and is very popular among developers for unit testing database-driven projects. In this blog I will discuss some of the tips to make your life easier and beautiful for DbUnit testing. 

**Using same connection for entire test suite** If you are extending DatabaseTestCase then this class creates a connection to the database in setUp() and closes it in teardown() this can prove to be a big overhead and can cause considerable slowdown in running tests as the number of test cases increase. Maintaining a single connection for the entire test suite can prove to be beneficial in such cases.

If you are using JUnit 4 then you can use annotations for performing initial operations (@BeforeClass) and thus there is no need to extend the DatabaseTestCase and finally handling the closing of database connection.

[java]@BeforeClass public static void setUpBeforeClass() throws Exception { handleSetUpOperation(); } [/java] If several test cases are using a read only dataset as expected state of the database after running the test cases then loading this expected dataset file only once for all the test cases can help up in speeding up things.

**Specifying a DTD for the dataset can act as a cure for some of the things** You can create a DTD by using the following code in DbUnit.

[java]FlatDtdDataSet.write(connection.createDataSet(), new FileOutputStream("test.dtd")); [/java]

This DTD can be used now to \- Validate a dataset file against it. \- Providing null values to a column by omitting it for a row in dataset.

**Keeping some reusable methods** A few reusable methods like asserting the database state after running tests with an expected state dataset file can be very useful. I am mentioning one of such methods here [java] protected void assertTables(String filename, String[] tableNames) throws SQLException, Exception, DataSetException, IOException, DatabaseUnitException { IDataSet expectedDataSet = null; if (tableNames == null) { // Load expected data from an XML data set expectedDataSet = new FlatXmlDataSet(new File(filename)); tableNames = expectedDataSet.getTableNames(); } else { // Load subset of the complete dataset expectedDataSet = new FilteredDataSet(tableNames, new FlatXmlDataSet(new File(filename))); } // Fetch database data after executing your code IDataSet actualDataSet = getConnection().createDataSet(tableNames); ITable[] expectedTables = new CompositeTable[tableNames.length]; for (int i = 0; i <tableNames.length; i++) { expectedTables[i] = new CompositeTable(expectedDataSet .getTableMetaData(tableNames[i]), actualDataSet .getTable(tableNames[i])); } Assertion.assertEquals(expectedDataSet, new CompositeDataSet( expectedTables)); } [/java]

Above method assertTables can assert the current state of the database after running test cases with the state provided in the dataset file. This method is intelligent enough to handle both cases of asserting the database state with the expected dataset file by supplying table names (might be a subset of all the tables provided in the dataset file) or without the table names in which case all the tables provided in the dataset files are used. The method uses table meta-data provided in the dataset file to get state of the corresponding table from the database, thus to ignore column such as auto-generated column can be automatically avoided in comparison by not mentioning them in the dataset file.

**Happy Red-Green-Refactor-ing.**