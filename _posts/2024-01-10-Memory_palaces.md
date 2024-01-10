# Making virtual memory palaces
Memory palaces are an ancient memory technique, where one places objects to remember along a path located in a physical location (a building, a house you know, etc...) Sometimes you might want to revisit a place to refresh its memory, or you might want to add new places to visit. One (clever) trick, as described in a this <a href="https://www.youtube.com/watch?v=FvTmqFgMSs8"> youtube video by Johannes Mallow </a>


is to use the virtual tours used by real estate companies (especially the Matterport tours). If you want to revisit on your own computer those tours, you might want to download locally those files and run the virtual tour yourself.
I keep forgetting the instructions to do it, so I decided to record them here instead.

## Steps to run locally a virtual tour
* download and extract the following to a local folder: <a href="https://github.com/rebane2001/matterport-dl"> matterport-dl </a>
* visit a page hosting virtual tours: <a href="https://www.luxuryrealestate.com/360-tours"> luxury tours </a>
* download locally a virtual tour : ```python3 matterport-dl.py [url_or_page_id]``` The matterport address usually appears like this: ```https://my.matterport.com/show/?m=2oNBQu1FVxA```
* To run locally, use ```python3 matterport-dl.py [url_or_page_id] 127.0.0.1 8080``` and go to  ```http://127.0.0.1:8080``` in a browser.




