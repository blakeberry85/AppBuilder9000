# Contents
* [AppBuilder9000](#appbuilder9000)
* [Index Page](#index-page)
* [Item Details](#item-details)
* [Edit and Delete Functions](#edit-and-delete-functions)

# AppBuilder9000
In my time at The Tech Academy I got the chance to work with a team of developers to design and build a website oriented around hosting apps relating to hobbies. This gave me valuable experience with not only the Django Framework and the technologies involved, but also how to work with other developers using Agile methodologies. I worked on multiple stories involving creating a database that users can save, edit and delete data to, as well as display an index of all the data saved to the database with the ability to view details of any item. I used a combination of bootstrap and my own styling on the website.


# Index Page
One of my stories was to create an index page that would display all the items that the user has entered into the database. I accomplished this by creating a function that grabs all the items in the database, displays them in table format, and puts them into a Paginator function, to prevent overly long pages when there are many items.

Python(views.py):

    def breakingbad_savedepisodes(request):
        episodes = Episode.Episodes.all()
        paginator = Paginator(episodes, 15)

        page_number = request.GET.get('page')
        page_obj = paginator.get_page(page_number)
        return render(request, "BreakingBadApp/BreakingBadApp_index.html", {'page_obj': page_obj})
        
HTML(index page):       
       
          {% for episode in page_obj %}
            <tr>
              <th scope="row"><a href="details-{{ episode.pk }}">{{ episode.episode_title|capfirst }}</a></th>
              <td>{{ episode.episode_season }}</td>
              <td>{{ episode.episode_number }}</td>
              <td>{{ episode.episode_date }}</td>
            </tr>
          {% endfor %}
          </tbody>
        </table>
            <nav>
              <ul class="pagination justify-content-center mb-2">
                <li class="page-item">
                  <a class="page-link text-dark" href="?page=1" tabindex="-1">First</a>
                </li>
                  {% if page_obj.has_previous %}
                    <li class="page-item"><a class="page-link text-dark" href="?page={{ page_obj.previous_page_number }}">{{ page_obj.previous_page_number}}</a></li>
                  {% endif %}
                <li class="page-item page-link bg-dark text-white">{{ page_obj.number }}</li>
                  {% if page_obj.has_next %}
                    <li class="page-item"><a class="page-link text-dark" href="?page={{ page_obj.next_page_number }}">{{ page_obj.next_page_number }}</a></li>
                  {% endif %}
                <li class="page-item">
                  <a class="page-link text-dark" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
                </li>
              </ul>
            </nav>        
        
    
# Item Details
The next story was to create a details page for my app, allowing the user to view specifics on any item in the database by clicking that item from the index. I did this by getting the item's primary key and using that to display details on a specific item.

Python(views.py):

    def breakingbad_details(request, pk):
    pk = int(pk)
    episode = get_object_or_404(Episode, pk=pk)
    details = {'episode': episode}
    return render(request, 'BreakingBadApp/BreakingBadApp_details.html', details)
    
HTML(details page): 

    <table class="table table-bordered form_container">
          <thead>
            <tr>
              <th scope="col">Episode Name</th>
              <th scope="col">Season #</th>
            </tr>
            <tr>
              <td>{{ episode.episode_title|capfirst }}</td>
              <td>{{ episode.episode_season }}</td>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="col">Episode #</th>
              <th scope="col">Air Date</th>
            </tr>
            <tr>
              <td>{{ episode.episode_number }}</td>
              <td>{{ episode.episode_date }}</td>
            </tr>
          </tbody>
        </table>

# Edit and Delete Functions
Another story involved allowing the user to edit or delete items from the database. I did this through the details page, the user could click the edit button to be taken to a form page with the current item data filled in as placeholders for reference, or they can click the delete button to remove the item completely from the database, with a confirmation box using a bootstrap modal to prevent accidental deletions.

Python(views.py):

    def breakingbad_edit(request, pk):
    pk = int(pk)
    episode = get_object_or_404(Episode, pk=pk)
    form = EpisodeForm(data=request.POST or None, instance=episode)
    if request.method == 'POST':
        if form.is_valid():
            form = form.save(commit=False)
            form.save()
            return redirect('BreakingBadSavedEpisodes')
        else:
            print(form.errors)
    else:
        return render(request, 'BreakingBadApp/BreakingBadApp_edit.html', {'form': form})


    def breakingbad_delete(request, pk):
        episode = get_object_or_404(Episode, pk=pk)
        episode.delete()
        return redirect('BreakingBadSavedEpisodes')
        
HTML(edit page):   

    <div class="d-flex justify-content-center form_container">
            <form method="POST">
                {% csrf_token %}
                {{form.as_p}}
                <button type="submit" class="btn btn-success mr-5">Save</button>
            </form>
        </div>
        
HTML(details page):

            <table class="table table-bordered form_container">
          <thead>
            <tr>
              <th scope="col">Episode Name</th>
              <th scope="col">Season #</th>
            </tr>
            <tr>
              <td>{{ episode.episode_title|capfirst }}</td>
              <td>{{ episode.episode_season }}</td>
            </tr>
          </thead>
          <tbody>
            <tr>
              <th scope="col">Episode #</th>
              <th scope="col">Air Date</th>
            </tr>
            <tr>
              <td>{{ episode.episode_number }}</td>
              <td>{{ episode.episode_date }}</td>
            </tr>
          </tbody>
        </table>
        <form class="d-flex justify-content-center form_container" action="edit-{{ episode.pk }}">
            <input type="submit" class="btn btn-primary mr-2" value="Edit">
            <input type="button" class="btn btn-danger ml-2" data-toggle="modal" data-target="#exampleModal" value="Delete">
        </form>

        <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog">
            <div class="modal-content">
              <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Are you sure?</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                  <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <div class="modal-body">
                Are you sure you want to delete this episode from the database?
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
                <form action="delete-{{ episode.pk }}">
                    <button type="submit" class="btn btn-danger">Delete</button>
                </form>
              </div>
            </div>
          </div>
        </div>
