# Code-Summary-C-Sharp Backend
##Introduction

My last two week sprint of the C# live project was centered around backend work. This would be the last sprint I 
participate in with The Tech Academy. At this point I went thought 3 different sprints and spent a decent amount of time
with C# so I wanted to jump into stories I was a little less familiar with to challenge myself. I probably got done the
least amount of stories during this spring compared to the other three but I also had the opportunity to work on the 
most challenging stories up to this point. When I hopped into back into the backend sprint, there was a bit of a shortage
of backend stories. Since the C# live project was a being developed for an actually client, at this point in the project
we spent most of our time wrapping up the loose ends of the project. 

### Week 1

##### The Problem #1

My first story was very simple. I had to replace our edit, delete and details links in our Jobs.index page with their 
corresponding glyphicons. 

##### The Solution #1

The code that I needed to do this was already available elsewhere in our code. All I had to do was compile the code 
scattered through our project. After gathering all the necessary code, I wrote the code that I have posted below.

           <td>
                    <a href="@Url.Action("Edit", "Jobs", new { id = item.JobId }, null)">
                        <span class="glyphicon glyphicon-pencil" aria-hidden="true"></span>
                    </a> |
                    <a href="@Url.Action("Details", "Jobs", new { id = item.JobId }, null)">
                        <span class="glyphicon glyphicon-info-sign" aria-hidden="true"></span>
                    </a> |
                    <a href="@Url.Action("Delete", "Jobs", new { id = item.JobId }, null)">
                        <span class="glyphicon glyphicon-trash" aria-hidden="true"></span>
                    </a>
                </td>

This was a simple story and I was happy to make quick work of it. It was at this point I noticed that I was developing
the ability to develop some amount of expectations of what a story would entail before I picked it up. I knew the 
outline of what I would have to do to solve a story even if I didn't know the details, at least in some cases. 

##### The Problem #2

My second story was a different kind of animal. This is an example of me not knowing how to solve a story at all before
I jumped into it but wanting to challenge myself in the final stretch of the live project. My task was to create a row
to allow a site admin to filter the site users by all respective criteria. Since we already had logic on our site to 
filter users, I figured I could utilize that code and go from there. Even though that was the case, this was still a 
frustrating story in the end.

##### The Solution #2
 
I first decided to start with the code I would need on the view. That code is below

     @using (Html.BeginForm(FormMethod.Post))
            {
                <tr>
                    <th>
                        @Html.DropDownList("UserName", ((ConstructionNew.Controllers.AccountController)this.ViewContext.
                        Controller).GetUserName(), "-- select --", new { @onclick = "filterReset('userName')" })
                    </th>
                    <th>
                        @Html.DropDownList("Fname", ((ConstructionNew.Controllers.AccountController)this.ViewContext.
                        Controller).GetFirstName(), "-- select --", new { @onclick = "filterReset('person')" })
                    </th>
                    <th>
                        @Html.DropDownList("LName", ((ConstructionNew.Controllers.AccountController)this.ViewContext.
                        Controller).GetLastName(), "-- select --", new { @onclick = "filterReset('person')" })
                    </th>
                    <th>
                        @Html.DropDownList("WorkCategory", ((ConstructionNew.Controllers.AccountController)this.
                        ViewContext.Controller).GetWorkCategory(), "-- select --", new { @onclick = "filterReset('person')" })
                    </th>
                    <th>
                        @Html.DropDownList("UserRole", ((ConstructionNew.Controllers.AccountController)this.
                        ViewContext.Controller).GetUserRole(), "-- select --", new { @onclick = "filterReset('person')" })
                    </th>
                    <th>
                    </th>
                    <td>
                        <input type="submit" value="Submit" onclick="return Search('Click ok filter')" />
                    </td>
                </tr>
            }
            
I also had to write the code that would populate each of these lists. Below is two examples of those code blocks. Since
this was a filter row, I used the function "Distinct()" to give me only one representation of whatever it was I was 
retrieving at the time.

         public IEnumerable<SelectListItem> GetUserName() 
        {
            // creates and returns a select list of UserName to be passed into a cshtml file to filter cshtml content
            return db.Users.Select(a => a.UserName).Distinct().OrderBy(o => o).Select(m => new SelectListItem()
            {
                Value = m,
                Text = m,
            });
        }

        public IEnumerable<SelectListItem> GetFirstName() 
        {
            // creates and returns a select list of distinct User first names to be passed into a cshtml file to filter 
            cshtml content return db.Users.Select(a => a.FName).Distinct().OrderBy(o => o).Select
            (m => new SelectListItem()
            {
                Value = m,
                Text = m,
            });
        }
       
The "GetUserRole" and "GetWorkCategory" had to be done a bit differently. Instead of referencing the bd.Users collection
as was done in the previous code blocks, I had to reference the db.Roles collection and get the Id and Name of each.

       
         public IEnumerable<SelectListItem> GetUserRole() 
        {
            // creates and returns a select list of roles to be passed into a cshtml file to filter cshtml content
            return db.Roles.Select(x => new SelectListItem()
            {
                Value = x.Id,
                Text = x.Name
            });
        }

Prior to me taking this story, GetWorkTypeDescriptions was a list of enums. Which, down the line I would have trouble 
filtering though as a list. I had to refactor this code to turn GetWorkTypeDescriptions into a dictionary to make the
filter row work effectively. Below is what I changed the code to so I could achieve this

        public static Dictionary<int, string> GetWorkTypeDescriptions()
        {
            // creates a dictionary for workTypes 
            var workList = new Dictionary<int, string>();
            // creates a for loop to iterate through workTypes and add each worktype as a value to the workList dictionary
            foreach (WorkType workType in Enum.GetValues(typeof(WorkType)))
            {
                workList.Add((int) workType, workType.GetDescription());
            }
            return workList;
        }


Carrying on, below is how I did the GetWorkCategory to work with the dictionary that I created. At this point all I had
to do was set the value to the key of each work category and convert it from an int to a string, which would later be 
converted back to a string but need to be a string to work with this part of the code. 

        public IEnumerable<SelectListItem> GetWorkCategory()
        {
            // creates and returns a select list of work types using the GetWorkTypeDescriptions function to be passed 
            into a cshtml file to filter cshtml content return Extensions.WorkTypeMethods.GetWorkTypeDescriptions()
            .Select(i => new SelectListItem() {
                // sets value of select item to the key of the workList dictionary
                Value = i.Key.ToString(),
                // sets text of select item to the value of the workList dictionary
                Text = i.Value
            }) ;
        }
        

My next step was to create a post function that would retrieve the data passed into the controller from the view and
perform the necessary logic on it to return the list that would become the filtered list to be passed back into the 
view. I knew I would have to be able to filter by multiple criteria so I figure that best way to do that would be to 
instantiate an empty list and through a series of conditional statements, slowly effect the contents of the list.
I first started with the variable "users" that was the entire list of users. Each conditional statement check to 
be sure that the field passed in was not empty or null first. If it was not then the lamda expression finds the users
where the current information being compared is equal to the information passed into the field. These conditional
statements continue to effect the list accordingly until we have the final list that is then passed back to the view.

     public ViewResult _UserListPartial(string UserName, string FName, string LName, string WorkCategory, 
     string UserRole)

        // creates list of users to be filtered down based on arguments passed in
        var users = db.Users.ToList();

        // creates conditional statement to check value of the UserName parameter and updates users list according to
         the argument passed in 
        if (!string.IsNullOrWhiteSpace(UserName))
        {
            users = users.Where(i => i.UserName == UserName).ToList();
        }
        // creates conditional statement to check value of the FName parameter and updates users list according to
         the argument passed in 
        if (!string.IsNullOrWhiteSpace(FName))
        {
            users = users.Where(i => i.FName == FName).ToList();
        }
        // creates conditional statement to check value of the LName parameter and updates users list according to
        the argument passed in 
        if (!string.IsNullOrWhiteSpace(LName))
        {
            users = users.Where(i => i.LName == LName).ToList();
        }
        // creates conditional statement to check value of the WorkCategory parameter and updates users list according
         to the argument passed in 
        if (!string.IsNullOrWhiteSpace(WorkCategory))
        {
            // converts value of WorkCategory back to an int to be compared to the key in the dictionary "workList"
            users = users.Where(i => (int)i.WorkType == Convert.ToInt32(WorkCategory)).ToList();
        }
        // creates conditional statement to check value of the UserRole parameter and updates users list according
         to the argument passed in 
        if (!string.IsNullOrWhiteSpace(UserRole))
        {   
            // updates list to include users whose role contains the selected user role passed into the UserRole parameter 
            users = users.Where(i => i.Roles.Select(j => j.RoleId).Contains(UserRole)).ToList();
        }
        return View(users);
        
### Week 2
        
##### The Problem #3

My next story in the back end project was another fun one. When a admin tried to delete a job that had was associated
with a schedule, It would also delete that schedule. This was how we wanted it but we didn't have anyway of letting the
user know the job they are about to delete would also delete the schedule. My task was to add logic that would tell
the user whether or not the job they are trying to delete is on a current or future schedule and inform them that the
related schedule will be deleted as well. 

##### The Solution #3
There was already a little bit of work that was completed for this task to begin with. The code below is the ground work
that was laid to start to solve this problem already.

     public ActionResult DeleteConfirmed(Guid id)
        {
            try
            {
                var job = db.Jobs.Include(x => x.Schedules).FirstOrDefault(x => x.JobId == id);
                db.Jobs.Remove(job);
                TempData["shortMessage"] = job.JobTitle.ToString() + " job has been deleted.";
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            catch (Exception)
            {
                TempData["shortMessage"] = "This job has people scheduled in it and therefore cannot be deleted.";
                return RedirectToAction("Index", "Jobs");
            }
            var job = db.Jobs.Include(x => x.Schedules).FirstOrDefault(x => x.JobId == id);
            db.Jobs.Remove(job);
            TempData["shortMessage"] = job.JobTitle.ToString() + " job has been deleted.";
            db.SaveChanges();
            return RedirectToAction("Index");
        }
        
 I figured I would start with writing the logic for the conditional statements first. I first needed to put the 
 schedules associated with job id into a variable that I could preform logic on. I did that with the line of code 
 below. 
     
        var job = db.Jobs.Include(x => x.Schedules).FirstOrDefault(x => x.JobId == id);
        var schedule = db.Schedules.Include(x => x.Job).FirstOrDefault(x => x.Job.JobId == id);

I put the below two blocks of code inside the try block.
        
            if (schedule.StartDate > DateTime.Today)
        {
        }
            if (schedule.StartDate <= DateTime.Today && schedule.EndDate > DateTime.Today)
        {
        }
        
This code worked on the back end. When the submit button is clicked, the code correctly displays the correct error 
message and will not let you delete the job but does tell whether or not it is associated with a current or past
schedule. 

Onto the cshtml for this story. I would need code to be able to give the user a confirmation box to deliver them the 
message and ask whether or not they still want to delete the job and it's associated schedules. There was javascript
code that was already written for this task specifically. Since clicking the submit button triggered the front end 
javascript code and the back end conditional error, I needed to find a way to not trigger the error and have the
confirmation message box successfully delete the database items on the ok button click. I didn't have to much practice
using javascript in these live projects so I was thankful for this story. At first I thought maybe I could trigger the
javascript on the backend. I did a bit of research on how to go about doing that but I couldn't find any definitive 
answer. I was very happy about the next part of the story because It solved the problem. 

I thought that maybe I could
pass in a variable via the viewbag that could be used in conjunction with the razor syntax to create conditional
statements to call the javascript accordingly. So that's exactly what I did. First I moved my conditional statements from
the post post.delete method to the get.delete method in the jobs controller so I could pass in logic to the page to
dynamically render its contents. Since the get method had the job id in question already being passed in, all of the
code I used in the post.delete method would still work in the get.delete method. Below is the code I used in the 
get.delete method. You'll see the comments below to give context to unfamiliar users. Because some jobs might not have
a schedule associated with the, the value of the variable schedule might be null. This would throw a error once we got
to the "if (schedule.StartDate > DateTime.Today)" line of code. To solve this I threw both conditional statements inside
a try catch loop to catch the null value error. In the event there are no schedules associated with an even, we will
not need to pass anything to the viewbag and we can just go right to the view as usual. If one of the "if" statements 
are triggered, we will pass in ether the value of "future" or "current" to the viewbag.

    
        // finds schedule associated with the job id that was passed into delete function to be used for
        // conditional statement to determine if schedule is future, current or past.
        var schedule = db.Schedules.Include(x => x.Job).FirstOrDefault(x => x.Job.JobId == id);

        // try catch block to avoid null error if job does not have a schedule with it. If schedule
        // is null inside try block it will trigger a null exception witch will put the program through to 
        // the view as it normally does.
        try
        {
            // checks to see if the startdate of the selected jobs associated schedule is after 
            // todays date. If so, if sets the value of the variable viewbag.onSchedule to the string "future"
            // to be passed to the cshtml file that will be used in a conditional statement to determine whether
            // or not the javascript for the "confirm message" gets triggered or not. 

            if (schedule.StartDate > DateTime.Today)
            {
                ViewBag.onSchedule = "future";
            }

            // checks to see it the startdate of the selected jobs associated schedules startdate is before
            // todays date and the schedules enddate is after todays date, indicating it is a current schedule. 
            // If so, if sets the value of the variable viewbag.onSchedule to the string "current"
            // to be passed to the cshtml file that will be used in a conditional statement to determine whether
            // or not the javascript for the "confirm message" gets triggered or not. If the schedules startdate AND
            // enddate is before todays date, this means the schedule has already passed and neither conditional statements
            // are met and the program continues normally.

            if (schedule.StartDate <= DateTime.Today && schedule.EndDate > DateTime.Today)
            {
                ViewBag.onSchedule = "current";
            }
        }

        // in the event there value of schedule is null, this catch block is hit and the program continues as usual
        catch (NullReferenceException)
        {
            return View(job);
        }
        return View(job);

Back to the front end side of the story. Since we are dynamically deciding the value of the viewbag on the back end, the
only thing I had to do was place conditional statements to decide the value of the messages in the javascript. Again,
you will find comments below to give context to the code. I learned that I had to add an id to the "Html.BeginForm"
that I would later reference when calling the function. I chose the if "deleteFormId". At that point I could begin my
conditional statements. Below is only 2 of the 3 conditional statements as an example. 


     @{ string deleteFormId = Guid.NewGuid().ToString();}
    <!-- creates the post form to delete selected job. assigns the id "deleteFormId" to the form to be reference later 
    in the java script function "modConfirm" -->
    @using (Html.BeginForm("Delete", "Jobs", FormMethod.Post, new { enctype = "multipart/form-data", id = deleteFormId }))
    {
        @Html.AntiForgeryToken()

        <!--This conditional statement checks the contents of viewbag.onSchedule. If the contents of the viewbag is 
        "future" this codeblock gets triggered-->
        if (ViewBag.onSchedule == "future")
        {
            <div class="form-actions no-color">
                <!-- Submit button for delete job. onclick attribute calls mobConfirm java script code to prompt 
                confirmation message. The first argument passed into the function is the message for the modal -->
                
                <!-- and the second argument getting passed into the function is the id of the form that is being 
                submitted. -->
                
                <!-- if this specific "if" statement gets triggered, the job is on a future schedule and the model 
                statement is adjusted accordingly-->
                <input type="submit" value="Delete" class="btn btn-default" onclick="modConfirm('Job is on a future 
                schedule. This will delete the associated schedule. Are you sure you want to delete these items?',
                '@(deleteFormId)')" /> |
                @Html.ActionLink("Back to List", "Index") |
                @Html.ActionLink("Back to Dashboard", "Index", "Dashboard")
                <div class="modal fade" id="confirmModal" tabindex="-1" role="dialog" aria-hidden="true">
                    <!-- this partial view is what gets triggered from the java script function "modConfirm" -->
                    @Html.Partial("~/Views/Shared/ConfirmMessageModalPartial.cshtml")
                </div>
            </div>
        }

        <!--This conditional statement checks the contents of viewbag.onSchedule. If the contents of the viewbag is 
        "current" this codeblock gets triggered-->
        if (ViewBag.onSchedule == "current")
        {
            <div class="form-actions no-color">
                <!-- Submit button for delete job. onclick attribute calls mobConfirm java script code to prompt 
                confirmation message. The first argument passed into the function is the message for the modal -->
                
                <!-- and the second argument getting passed into the function is the id of the form that is being 
                submitted-->
                
                <!-- if this specific "if" statement gets triggered, the job is on a current schedule and the model 
                statement is adjusted accordingly-->
                
                <input type="submit" value="Delete" class="btn btn-default" onclick="modConfirm('Job is on a current 
                schedule. This will delete the associated schedule. Are you sure you want to delete these items?',
                '@(deleteFormId)')" /> |
                @Html.ActionLink("Back to List", "Index") |
                @Html.ActionLink("Back to Dashboard", "Index", "Dashboard")
                <div class="modal fade" id="confirmModal" tabindex="-1" role="dialog" aria-hidden="true">
                    <!-- this partial view is what gets triggered from the java script function "modConfirm" -->
                    @Html.Partial("~/Views/Shared/ConfirmMessageModalPartial.cshtml")
                </div>
            </div>
        }
        
 These code blocks wrapped up the story and had everything working as planned. Again, I learned a lot from this story
 and had a lot of fun doing it.
 
 ##### The Problem #4
 
 My last story in the sprint was an absolute test for sure. My task was to finish implementing the drag an drop 
 functionality in the create weekly schedules page. This one took me down a bit of a rabbit hole. I really had to study
 the code that was already written to get a good idea of what still had to be done and what I had to do to solve it.
 
 
 ##### The Solution #4
 
 Initially, the we had javascript code that was returning a string that was being turned into a list on the backend.
 The idea was that it would return a list of jobs with the employees who were assigned to that job coming after the name
 of the job in the list.
 
      [HttpPost]
        public ActionResult GetData(string[] namesArr, string weeks, Job job)
        {
            // identifies which string to use as a seperator for date range 
            string[] stringSeperator = new string[] { "to" };
            
            //creates a string array for start and end date
            string[] dateRange = weeks.Split(stringSeperator, StringSplitOptions.None);
            string startDate = dateRange[0];
            string endDate = dateRange[1];
            
            // identifies all unnecessary characters from string to allow for proper parsing
            char[] myChar = { '[', ']' };
            
            // converts JSON string to regular string
            string employees = namesArr[0];
            
            // removes unnecessary character from string
            string newEmployee = employees.Trim(myChar);
            
            // parses the string and sends each item (job and employees) to an array
            string[] employeeList = newEmployee.Split(',');
            
            //converts array to a list to allow for easy removal of items
            var listEmployees = employeeList.ToList<string>();
            return null;
        }
        
 My first thought was to check each item in the list and if it was a job perform one set of actions and if it was an
 employee perform another set of actions. Below is the code I used in attempts to do this. It worked nicely to some
 extent but the information I was getting from the "listEmployees" list was formatted in a way that the conditional
 statements would not get hit. 
 
        foreach (var word in listEmployees)
        {
            List<string> userList = new List<string>();
          
            foreach (var dbJobs in GetJobs())
            {
                if (dbJobs.JobTitle.Contains(word.TrimEnd().TrimStart()))
                {
                    assignedJob = word;
                }
             
                foreach (var dbUser in GetUsers())
                {
                    if (word == dbUser.ToString())
                    {
                         userList.Add(word);
                    }
                }
            }
            if (assignedJob != "" && userList.Count > 0)
            {
      
                foreach (string user in userList)
                {
                    foreach(ApplicationUser applicationUser in db.Users)
                        if (user == applicationUser.UserName)
                        {
                            ApplicationUser currentUser = applicationUser;
                            DateTime currentStartDate = DateTime.Parse(startDate);
                            DateTime currentEndDate = DateTime.Parse(endDate);
                            Schedule schedule = new Schedule(currentUser, job, currentStartDate, currentEndDate);
                            db.Schedules.Add(schedule);
                            db.SaveChanges();
                        }
                }
            }
        }
        
 It was at this point I began to think about another solution, so I went back to the cshtml page where the javascript
 was writing the string that was being turned into a list on the backend. If I could find a way to turn the list that
 was being sent to the controller into more a dictionary I could more easily parse through it. I also wanted to be
 sending through the job and user IDs instead of the job title and the user names. After a bit of research, I learned
 what and how I needed to target the html for the javascript. I had a bit of difficulty grabbing the the job names from
 the initial html so created a code block for the purpose of collecting the jobs for the script. The code block for both
 the html and the javascript are below.
 
       <div style="display: none">
                <!-- creates id "jobIds" to be targeted later by scripting-->
                <ul id="jobIds">
                    <!-- creates for loop to get list of jobs in jobs model to be-->
                    @foreach (Job job in Model.Jobs)
                    {
                        <li>@Html.DisplayFor(modelItem => job.JobId)</li>
                    }
                </ul>
            </div>
 
       //collects all instances of job and places them in an array: valuearray
       // "getElementById grabs the contents of the all of the children inside the div with the id "jobsIds"
       var jobIds = document.getElementById("jobIds").children;
 
 After I had the jobs, I needed to grab the employees that were dragged into each respective job container. This was 
 done the similar to getting the jobs. In this case, I had to make the targeting of the divs script IDs dynamic so that
 each job container that gets rendered be referred to individually. The code block below is how tht was achieved with 
 the div id "id="@Html.DisplayFor(modelItem => job.JobId)-workers>"
 
         <!-- creates an id on each job container to that is used to detect if any users have been dragged into-->
                    <!-- as the loop iterates to create the containers, the id will be dynamically changed to whatever jobId-->
                    <!-- is associated with that iteration -->
                    <!-- the container by the script when the submit button is clicked -->
                    <ul class="sortable_list connectedSortable" id="@Html.DisplayFor(modelItem => job.JobId)-workers">
                        <li id="no">Drag User Here</li>
                    </ul>
 
 The last item that needed script targeting was the drop down menu select list. That line of code is below.
 
        <!--creates SelectList for drop down list and assigns the id "weekDDL" for script targeting-->
        @Html.DropDownList("weeks", ViewBag.weeks as SelectList, new { @id = "weekDDL" })
    
Now that the date range, user.id and job.id now had script targeting IDs, I could perform the necessary logic on them.
Below is the javascript that created the json object with the jobs as the key and all of their corresponding employees 
dragged to that job as the list for the objects key. The comments in the code block with put context to the conditional
statements and variable declarations to any unfamiliar user. 
 
    document.getElementById("getId").onclick = function () {
        // creates list object to be later populated by conditional statements
        var jobList = [];

        //gets selected value of dropdown for week
        var week = $('#weekDDL :selected').text();
        
After the list object is created and the selected week range is retrieved from the targeting id, a for loop is created 
to iterate over the as many jobs as there are in the database. The "job" variable is a key value pair that will 
eventually be placed into the "jobList" list.
  
        // creates for loop to iterate over the length of jobIds 
        for (var j = 0; j < jobIds.length; j++) {
        
            // creates object "job" that will act as a key, value pair in the list object "jobList"
            var job = {};
            
After, the key in the "job" variable object is set to the id of whatever iteration of the list of jobs it is on and 
sets it to the value of that text. 
            
            // assigns the current value(job) of whatever iteration we are on to the value job.jobId
            // this will act as the key in the object "job"
            // this also syncs up the job being iterated over for tagerting ids both "jobIds" and "job.jobId + '-workers'"
            job.jobId = jobIds[j].textContent;
            
After, the the value "employees", which is for now an empty list, is set for the object variable "job". Any employees
that have been dragged over to the container of whatever current iterate of job it is on will be placed in the 
job.employees list. If there are no employees in the container, the list will remain empty.
            
            // creates an empty list to be populate by conditional statment and loop below
            job.employees = [];
            
            // collects any employees that have been dragged over to the container with the id "job.jobId + '-workers'" 
            and puts them in a list
            
            // the for loop in the "foreach (Job job in Model.Jobs)" dynamically sets each "job.jobId" to each jobs job 
            id
            
            // ".children" collects all divs inside of the parent div with the id "job.jobId + '-workers'"
            var empList = document.getElementById(job.jobId + '-workers').children;
            
Because there is addition content in the div that contains the employees, we need to offset all the logic calculations 
by 1 to accommodate that. You will see that reflected in the comments. After the assigned employee data is retrieved,
the "job" variable object is placed in the "jobList" list. When all for loops are complete of both the jobs and assigned
employees, the "jobList" list is converted to a Json string.
            
            // the conditional statement checks length of the empList to see it it is longer then 1
            // because the div "<li id="no">Drag User Here</li>" is already present as a child in the div, we want to 
            check to be sure there is more then one div
            
            // if so, this block get triggered
            if (empList.length > 1) {
            
                // creates a for loop that iterates over the length of "empList" 
                // we set the value of i to 1 to compensate for the aformentioned div "<li id="no">Drag User Here</li>",
                 which we do not want to interpretate as an employee name
                for (var i = 1; i < empList.length; i++) {
                
                    // adds the current value the variable i to the list job.employees
                    // loop will add each employee that is a child of the parent div with the id "job.jobId + '-workers'"
                    job.employees.push(empList[i].id);
                }
            }
            // adds the object "job" to the list "jobList". This job will have at least the name of the job and if there
             were any employees that were dragged into the container
             
            // the for loop and conditional statements above would add them to the list associated with each job. If not
             the job.employee list will be empty and we will know
             
            // no users were associated with that job.
            jobList.push(job);
        }
        //converts value array to a JSON string
        var jobListJson = JSON.stringify(jobList)

After I had the json object, I needed to collect the selected date range and retrieve the value associated with the 
selected option.

        var e = document.getElementById("weekDDL");
        var daterange = e.options[e.selectedIndex].value;
        console.log(daterange);

After I had the data for both the jobs, their associated employees and the date range, I passed the json object to the
controller. That code block Is below. 

        //makes AJAX call to controller with JSON strings: 1 containing all jobs and employees assigned to each job, 1 
        with date range for schedule
        $.ajax({
            type: "POST",
            url: "/Schedules/GetData",
            datatype: "JSON",
            data: {
                jobPost: jobListJson,
                dateRangePost: daterange
            },
        });
    }
    
Back to the controller side of the action! Once the variables "jobPost" and "dateRangePost" have been retrieved from the
cshtml files script. I could perform the necessary logic on them. I first had to make a class to split the soon to be
deserialized and parsed json "jobPost" object into. That was done with the code block below.
    
         // creates the class "JobPost" for the json object to be parsed into
        public class JobsPost
        {
            public Guid JobId { get; set; }
            public List<string> Employees { get; set; }
        }

After That was done, I split the "dateRangePost" object on the "-" character and turned the start date and end date
to the their own variables.

     [HttpPost]
            // GetData function to retrieve data from MasterScheduleEdit.cshtml page to create weekly scedule
            public ActionResult GetData(string jobPost, string dateRangePost)
            {
                // creates variable jobs and sets it to the value of the deserialized Json object "jobPost" into the 
                variable "jobs".
                var jobs = JsonConvert.DeserializeObject<JobsPost[]>(jobPost);
                
                // creates the variable dates and sets it to the value of the "dateRangePost" that was passed in from 
                the view and splits it
                
                // on the char "-"
                var dates = dateRangePost.Split('-');
                
                // creates the variable starteDate and sets it to the value of the array item at index "0", which is the
                 start date that was passed in
                var startDate = DateTime.Parse(dates[0]);
                
                // creates the variable endDate and sets it to the value of the array item at index "0", which is the
                 end date that was passed in
                var endDate = DateTime.Parse(dates[1]);
    
After, I gathered the list of jobs and users with our methods we created to do so. Very good lesson. Then I created 
a for loop to iterate over the each job in the "jobs" object, retrieve the job and the employees associated with it and 
store them inside the "dbJobs" and "employees" to have logic performed on them.
    
                var dbJobs = GetJobs();
                var dbUsers = GetUsers();
                //iterate through posted jobs
                foreach(var j in jobs)
                {
                    // creates the variable dbJod and sets it to the job that matches the current jobId of whatever job
                     the iteration is on
                    var dbJob = dbJobs.Find(i => i.JobId == j.JobId);
                    
                    // creates variable employees and sets it to the user that matches the userId in the list of the
                     jobs object Employees, if there were any passed in at all
                    var employees = j.Employees.Select(e => dbUsers.Find(u => u.Id == e));
                    
After this was done, I wanted a way to check to see if a job is on the schedule during the selected we week. The 
line of code below creates the variable "currentSchedules" that is ether empty, or contains the schedules associated with
the current job being iterated if the job id matches and the start date and end date match with the start date and end 
date passed in. Each individual employee on that schedule for that time counts as it's own current schedule so they
can be dealt with accordingly.          
                    
                    // get current schedules that exist for this job providing the start date and the end date are the
                     same and puts it to a list.                      
                    // if there are none this will be empty. This will be used later when determining if the information
                     needs to be overwriten or not
                    var currentSchedules = db.Schedules.Where(i => i.Job.JobId == j.JobId &&
                    (i.StartDate >= startDate && i.StartDate <= endDate) &&
                    (i.EndDate <= endDate && i.EndDate >= startDate)).ToList();
                    
After, I iterated through the employees associated with the job in the current job object being iterated through. For
each employee, there is a conditional statement checking to see it that employee is on the current schedule list. Again,
the current schedule variable will have the current schedule only if the current iteration of the job matches and the 
date range selected by the user. If the conditional statement is triggered, we check the start and end date against the
current schedules start and end date to see if they need updating. If not, the variable sch is removed from the current
schedule list. If the current schedule variable is empty, the else block gets triggered and a schedule with the current
employee and current job and the selected start dates gets made. If there is a value in the variable "currentSchedules",
but no employees associated with it currently passed in the job object, the "var e in employees" foreach loop is skipped
and the "var c in currentSchedules" foreach is hit where the values in the "currentSchedule" list is removed from 
the database. If an employee was once on one that currentSchedule and no is not, the "var e in employees" foreach loop is 
skipped. The list of employees associated with that job will be empty and all the values in the list "currentSchedules" 
will be removed from the database. If there was once multiple employees on that current schedule but not there is half 
that amount, the "currentSchedules.Any(i => i.Person.Id == e.Id" block will get triggered and the variable "sch" with 
represent that current employees schedule and keep them specifically on the schedule. If an employee was once on the 
schedule but is now removed, "currentSchedules.Any(i => i.Person.Id == e.Id" block will not get triggered and they 
specifically will be deleted from the schedule.
                    
                    //iterate through employees associated with current job, if there are any at all.
                    foreach (var e in employees)
                    {
                        //check if any of the schedules in the DB are for the current employee
                        if (currentSchedules.Any(i => i.Person.Id == e.Id))
                        {
                            //get the schedule of the employee aleady assigned to the job
                            var sch = currentSchedules.First(i => i.Person.Id == e.Id);
                            //make sure dates match from our posted dates - update if necessary
                            if (sch.StartDate != startDate || sch.EndDate != endDate)
                            {
                                sch.StartDate = startDate;
                                sch.EndDate = endDate;
                                db.Entry(sch).State = EntityState.Modified;
                                db.SaveChanges();
                            }
                            //remove past schedule from list for later comparison of deleted schedules
                            currentSchedules.Remove(sch);
                        }
                        else
                        {
                            //if not an existing schedule, create a new one
                            db.Schedules.Add(new Schedule(e, dbJob, startDate, endDate));
                            db.SaveChanges();
                        }
                    }
                    //any schedules left in currentSchedules need to be removed (a person was unscheduled)
                    foreach(var c in currentSchedules)
                    {
                        db.Schedules.Remove(c);
                        db.SaveChanges();
                    }
                }
               
                return null;
           
The way this function is set up allows users to also edit a current schedule, not just create one. Again, this one was
a rabbit hole, but a fun rabbit hole.