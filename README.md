# Introduction

![cropped](https://user-images.githubusercontent.com/12884117/148686503-d2862da6-d0c9-49c2-9ab4-01d021ac3717.gif)

This is a crawler I wrote in Python using the APIs of Telethon months ago. This tool was not intended to be publicly available for a number of reasons, but eventually I decided to distribute it "as it is". Any contribution to the project is more than welcome :)

# Installation

Python 3.8.2 and Telethon 1.21.1 are required (along with other common packages, just read the imports), I don't guarantee it works with newer versions of Telethon.

To install Telethon just read their [documentation](https://docs.telethon.dev/en/latest/basic/installation.html), while to install this repository just `git clone` and run `python3.8 scraper.py` *only after* configurated the script properly (next section).

### Configuration

To use this tool you have to first obtain an API ID and an API HASH from Telegram: you can do this by following [this](https://my.telegram.org/auth) page.
Once done the ID and the HASH can be inserted into the code and the script can be launched. The first time it runs, it will ask to insert the telephone number.

# Usage

![init_empty](https://user-images.githubusercontent.com/12884117/148644423-5be39679-f1a5-4ec8-8597-95aa554cd6d9.gif)

In the code, there are two methods to initialize the crawler: `init_empty()` and `init()`. The former is used for the very first time that the script has been launched, while the latter is needed only in specific situations (read the code for details). Once the crawler has been launched with `init_empty()` and terminated, it basically processed all the groups/channels where the account is already in, collecting all the links shared in the chats along with a number of other data such that:

1. Name of the group/channel
2. Username
4. List of members (just for groups)
5. List of the last *n* messages
6. Other metadata...

<img width="1117" alt="groups" src="https://user-images.githubusercontent.com/12884117/148424182-80aa92c6-c501-481c-bbf6-4552a0c16bac.png">

These information have been saved in a pickle file called `groups`. Other files given in output are `to_be_processed` and `edges`. The former is a list of links that will be processed in the next iteration (see later) and the other one is a list of tuples of the form (group id, [group id list]) where the first entry represents the so called *destination vertex* and the list represents the *origin vertices*. Indeed, this uncommon data structure is the edge list of the *search graph* produced by the crawler (this is useful for data mining purposes, for instance I used it to perform link prediction between groups/channels exploiting both the graph structure and the messages). Probably is not the best data structure, since you have to reverse it later on if you want to actually use it for other tasks, but it is faster than other solutions to update.

Once the initialization is complete, you can comment `init_empty()` and uncomment `start()` in `main()` to process the new links collected before. This will generate three new files: `groups2`, `to_be_processed2` and `edges2`. Now you have to merge the old files with the new ones (I actually have a script that does that, but it is customized according to my environment so I will publish it as soon as I have time to generalize it): pay attention to the to_be_processed file, because you don't want to process it entirely in each run. You need to divide it since it will take too long, indeed there are some limitations to process a lot of groups, therefore you want to play smarter... next section.

# Limitations

Telegram doesn't want that you play with users' data therefore for each account, if I recall correctly, you can join 25 groups per hour, then Telegram will stop you. The script handles this, so if your usage is not so intensive it will not make your task unfeasible. But if you need hundreds or thousands of groups, well, you have to parallelize the script. I won't publish the code for doing this, but it is not so difficult to make this idea practical.

# Citation

This repository is released under the MIT license. If this work helped you somehow and you are going to use my code, my dataset, or derivations of them I would be glad to be cited in you project/research ("Edoardo Gabrielli" + link of the repo). Thank you!
