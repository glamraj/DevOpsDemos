Selenium Python Small Sample Project 1 | Unit Test, HTML Reports

###pip install html-testRunner

###C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>pip freeze
Could not parse requirement: -elenium
html-testRunner==1.2
Jinja2==2.9.5
MarkupSafe==1.1.1
selenium==3.141.0
urllib3==1.24.1

###C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>pip list
Package         Version
--------------- -------
-elenium        3.141.0
html-testRunner 1.2
Jinja2          2.9.5
MarkupSafe      1.1.1
pip             19.0.3
selenium        3.141.0
setuptools      40.8.0
urllib3         1.24.1

###C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>pip show html-testRunner
Name: html-testRunner
Version: 1.2
Summary: A Test Runner in python, for Human Readable HTML Reports
Home-page: https://github.com/oldani/HtmlTestRunner
Author: Ordanis Sanchez Suero
Author-email: ordanisanchez@gmail.com
License: MIT license
Location: c:\program files\python37\lib\site-packages
Requires: Jinja2
Required-by:

###C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>pip check html-testRunner
No broken requirements found.

###Script:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
from selenium import webdriver
import unittest
import HtmlTestRunner

class GoogleSearch(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        cls.driver = webdriver.Chrome(executable_path='../Drivers/chromedriver.exe')
        cls.driver.implicitly_wait(10)
        cls.driver.maximize_window()

    def test_search_automationstepbystep(self):
        self.driver.get("https://google.com")
        self.driver.find_element_by_name("q").send_keys("Automation Step by Step")
        self.driver.find_element_by_name("btnK").click()

    def test_search_raghav(self):
        self.driver.get("https://google.com")
        self.driver.find_element_by_name("q").send_keys("Rajib Chowdhury")
        self.driver.find_element_by_name("btnK").click()

    @classmethod
    def tearDownClass(cls):
        cls.driver.close()
        cls.driver.quit()
        print("Test Completed")

# To execute in CMD: C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>python -m unittest SeleniumTestRaghav.py

# To run without additional parameter in CMD: C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>python SeleniumTestRaghav.py
if __name__ == '__main__':
    # unittest.main()
    unittest.main(testRunner=HtmlTestRunner.HTMLTestRunner(output='C:/Users/rajib/PycharmProjects/FirstSeleniumTest/TestReports'))
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

# To execute in CMD: C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>python -m unittest SeleniumTestRaghav.py

# To run without additional parameter in CMD: C:\Users\rajib\PycharmProjects\FirstSeleniumTest\Test>python SeleniumTestRaghav.py
if __name__ == '__main__':
    unittest.main()
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
###HtmlTestRunner

import HtmlTestRunner
import unittest


class TestStringMethods(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('foo'.upper(), 'FOO')

    def test_error(self):
        """ This test should be marked as error one. """
        raise ValueError

    def test_fail(self):
        """ This test should fail. """
        self.assertEqual(1, 2)

    @unittest.skip("This is a skipped test.")
    def test_skip(self):
        """ This test should be skipped. """
        pass

if __name__ == '__main__':
    unittest.main(testRunner=HtmlTestRunner.HTMLTestRunner(output='C:/Users/rajib/PycharmProjects/FirstSeleniumTest/TestReports'))
	
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

from selenium import webdriver
import time
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome(executable_path='../Drivers/chromedriver.exe')
driver.implicitly_wait(10)
driver.maximize_window()

driver.get("https://opensource-demo.orangehrmlive.com/")

driver.find_element_by_id("txtUsername").send_keys("Admin")
driver.find_element_by_id("txtPassword").send_keys("admin123")
driver.find_element_by_id("btnLogin").click()
driver.find_element_by_id("welcome").click()
driver.find_element_by_link_text("Logout")
time.sleep(10)


driver.close()
driver.quit()
print("Test Completed")

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

C:\Users\rajib\PycharmProjects\FirstSeleniumTest>python -m SampleProject.SamplePOM.Tests.login