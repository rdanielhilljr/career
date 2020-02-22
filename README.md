# Live Project

## Introduction
For the last two weeks at The Tech Academy I have been part of a team working on a live Python project. Our project was created using Python, Django, SQLite, as well as HTML/CSS. The purpose of this sprint was to create software designed to help hobbyists keep track of their collections of items, as well as interact with APIs and use DataScraping to get up to date information on things related to those collections and hobbies. Specifically, my task were to create a web application that would have a database collection manager and implement Beautiful Soup to do data scraping to find relevant news stories related to the user. My app was designed for Portland Trailblazer apparel collectors/enthusiast to list items, search the database for current apparel, and also receive updated relevant news stories related to the team. My stories are listed below along with code snippets and descriptions.

Over the two week sprint I also had the opportunity to learn a great deal about project management utilizing an Agile/Scrum methodology and DevOps using Azure DevOps. I really enjoyed the life cycle of the project, in particular the daily standups, code retrospectives, and the code summary. I found it very beneficial to share what I had learned in debugging the project with my team and loved getting feedback and information from them as well. I will abosolutely use this expereince in my career going forward. 
 

## My Stories
* Database Collection Manager
* DataScraping using Beautiful soup


### Database Collection Manager

```python
# View function to add a new apparel to the database
def create(request):
    form = ApparelForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('Blazer_inventory')
    else:
        print(form.errors)
        form = ApparelForm()
    return render(request, 'Blazer_Ball/Blazer_Ball_create.html', {'form': form})

# View function that controls the main index page - list of apparel
def inventory(request):
    get_apparel = Apparel.Blazer_Apparel.all()
    context = {'Clothes': get_apparel}
    return render(request, 'Blazer_Ball/Blazer_Ball_inventory.html', context)

# View function to look up the details of an item(apparel)
def details_apparel(request, pk):
    pk = int(pk)                                # Casts value of pk to an int so it's in the proper form
    apparel = get_object_or_404(Apparel, pk=pk)   # Gets single instance of the jersey from the database
    context={'apparel':apparel}                   # Creates dictionary object to pass the jersey object
    return render(request,'Blazer_Ball/Blazer_Ball_details.html', context)

# view function to edit entry
def edit(request, pk):
    pk = int(pk)
    apparel = Apparel.Blazer_Apparel.get(pk=pk)
    form = ApparelForm(data=request.POST or None, instance=apparel)
    if request.method == 'POST':
        if form.is_valid():
            form.save()
            return redirect('Blazer_details', pk)
        else:
            print(form.errors)
    else:
        return render(request,'Blazer_Ball/Blazer_Ball_edit.html', {'form':form})

# View function for deleting ab item/apparel
def delete(request, pk):
    pk = int(pk)
    apparel = Apparel.Blazer_Apparel.get(pk=pk)
    context = {'apparel': apparel}            # Sets the item to a dictionary item for the template
    if request.method == 'POST':            # If the user posts a form, in this case just a delete button
        apparel.delete()                     # Deletes the item from the database
        return redirect('Blazer_inventory')            # Redirects back to the index
    else:
        return render(request, 'Blazer_Ball/Blazer_Ball_delete.html', context)
```

### DataScraping using Beautiful soup

```python
# View function for data scraping the MLS website for current news stories
def blazer_news(request):
    source = requests.get("https://www.blazersedge.com/")     # Get blazersedge.com/news as an html document
    print(source.status_code)                   # Used for debugging to ensure a 'success' code of 200
    soup = BS(source.content, 'html.parser')    # Initial processing of the html by beautiful soup, soup is now a navigatable object
    nodes = soup.find_all(class_="c-entry-box--compact__title")  # Search for divs with class node-title
    articles = []                               # Create blank array to add articles to
    for node in nodes:                          # Iterates through all the objects with class of node-title
        title = node.find('a').get_text()       # Sets title equal to the text of the a tag
        link = node.find('a').get('href')       # Sets link equal to the href of the a tag
        url = "" + link       # Modifies the link to a full url, since the links were relative
        article={'title':title, 'url': url}  # Creates and article object dictionary with needed elements
        articles.append(article)            # Adds article dictionary item to the array before iterating through next node
    context={'articles': articles}          # Creates a dictionary element for the articles to pass to the template
    return render(request, 'Blazer_Ball/Portland_Trailblazers_news.html', context)
```





