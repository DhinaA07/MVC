using EntityProject.Models;
using EntityProject.Models.ViewName;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.Mvc;

namespace EntityProject.Controllers
{
    public class ValuesController : Controller
    {
        WebProjectEntities db = new WebProjectEntities();


        public ActionResult Index()
        {
            List<Cricketers> cricketers = db.Crickets.Select(s => new Cricketers
            {
                PlayerId = s.PlayerId,
                PlayerName = s.PlayerName,
                Roles = s.Roles,
                Batting = s.Batting,
                Bowling = s.Bowling,
                Hightest_score = s.Hightest_score,
                wickets = s.wickets,
            }).ToList();
            return View(cricketers);
        }


        [HttpGet]
        public ActionResult Add()
        {
            return View();
        }


        [HttpPost]
        public ActionResult Add(Cricket cricketer)
        {
            if (ModelState.IsValid)
            {
                try
                {
                    db.Crickets.Add(cricketer);
                    db.SaveChanges();

                    return new JsonResult
                    {
                        Data = cricketer,
                        JsonRequestBehavior = JsonRequestBehavior.AllowGet
                    };
                }
                catch (Exception ex)
                {
                    return new JsonResult
                    {
                        Data = ex.ToString(),
                        JsonRequestBehavior = JsonRequestBehavior.AllowGet
                    };
                }
            }

            return View();
        }



        public ActionResult Edit(int PlayerId)
        {
            var cricketer = db.Crickets.FirstOrDefault(x => x.PlayerId == PlayerId);
            if (cricketer == null)
            {
                return HttpNotFound();
            }

            return View(cricketer);
        }


        [HttpPost]
        public ActionResult Edit(Cricket cricketer)
        {
            if (ModelState.IsValid)
            {
                try
                {
                    var existingCricketer = db.Crickets.FirstOrDefault(x => x.PlayerId == cricketer.PlayerId);
                    if (existingCricketer != null)
                    {
                        existingCricketer.PlayerName = cricketer.PlayerName;
                        existingCricketer.Roles = cricketer.Roles;
                        existingCricketer.Batting = cricketer.Batting;
                        existingCricketer.Bowling = cricketer.Bowling;
                        existingCricketer.Hightest_score = cricketer.Hightest_score;
                        existingCricketer.wickets = cricketer.wickets;

                        db.SaveChanges();

                        //return RedirectToAction("Index");
                    }
                    else
                    {
                        return HttpNotFound();
                    }
                }
                catch (Exception ex)
                {
                    return new JsonResult
                    {
                        Data = ex.ToString(),
                        JsonRequestBehavior = JsonRequestBehavior.AllowGet
                    };
                }
            }

            return View(cricketer);
        }



        public ActionResult View(int PlayerId)
        {
            if (ModelState.IsValid)
            {
                try
                {
                  
                    var cricketplayer = db.Crickets.FirstOrDefault(y => y.PlayerId == PlayerId);

                   
                    if (cricketplayer != null)
                    {
                        return View(cricketplayer);  
                    }
                    else
                    {
                        return HttpNotFound();  
                    }
                }
                catch (Exception ex)
                {
                    return new JsonResult
                    {
                        Data = ex.ToString(),
                        JsonRequestBehavior = JsonRequestBehavior.AllowGet
                    };
                }
            }

            return View();
        }
        [HttpPost]
        [ActionName("Delete")] 
        public ActionResult DeleteConfirmed(int PlayerId)
        {
            try
            {
                var cricketer = db.Crickets.FirstOrDefault(x => x.PlayerId == PlayerId);
                if (cricketer != null)
                {
                    db.Crickets.Remove(cricketer);
                    db.SaveChanges();
                    return RedirectToAction("Index");
                }
                else
                {
                    return HttpNotFound();
                }
            }
            catch (Exception ex)
            {
                return new JsonResult
                {
                    Data = ex.ToString(),
                    JsonRequestBehavior = JsonRequestBehavior.AllowGet
                };
            }
        }



    }
}


//Js 
function add() {
    location.href = "/Values/add";
}
 
function Edit(playerId) {
    location.href = "/Values/Edit?PlayerId=" + playerId;
}

function View(playerId) {
    location.href = "/Values/View?PlayerId=" + playerId;
}


function back()
{
    location.href = "/Values/Index";
}

function clickbutton() {
   
    let Players =
    {
        PlayerName: $("#playerName").val(),
        Roles: $("#roles").val(),
        Batting: $("#batting").val(),
        Bowling: $("#bowling").val(),
        Hightest_score: $("#highest").val(),
        wickets: $("#wickets").val(),
        PlayerId: $("#PlayerId").val(),
    }

    debugger;
    $.ajax({
   

        url: "/Values/add",
        type: "POST",
        data: {
            cricketer: Players
        },
        datatype: 'json',

        success: function (data) {
            alert("Added members");

            Swal.fire({
                title: data,
                text: "sucessfully added",
                icon: "success",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",


            }).then((result) => {

                if (result.isConfirmed) {
                    location.href = "/Values/Index";
                }

                else {
                    location.href = "/Values/add";
                }
            });

        },
        error: function (xhr, err) {
            alert(err);


        },

    })

}

function clickbutton1() {

    let PlayersEdit =
    {
        PlayerName: $("#PlayerName").val(),
        Roles: $("#Role").val(),
        Batting: $("#Batting").val(),
        Bowling: $("#Bowling").val(),
        Hightest_score: $("#HighestScore").val(),
        wickets: $("#Wickets").val(),
        PlayerId: $("#PlayerId").val(),
    }

   
    $.ajax({


        url: "/Values/Edit",
        type: "POST",
        data: {
            cricketer: PlayersEdit
        },
        datatype: 'json',
     
        success: function (data) {
          /*  alert("Added members");*/

            Swal.fire({
                title: "success",
                text: "successfully added",
                icon: "success",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",


            }).then((result) => {

                if (result.isConfirmed) {
                    location.href = "/Values/Index";
                }

                else {
                    location.href = "/Values/Edit";
                }
            });

        },
        error: function (xhr, err) {
            alert(err);


        },

    })

}


function deletePlayer(PlayerId) {

    if (!PlayerId) {
        alert("Player ID is required to delete.");
        return;
    }

    
    $.ajax({
        url: "/Values/DeleteConfirmed",
        type: "POST",
        data: { PlayerId: PlayerId }, 
        datatype: 'json',
        success: function (data) {
            Swal.fire({
                title: "Player Deleted",
                text: "The player was successfully deleted.",
                icon: "success",
                showCancelButton: true,
                confirmButtonColor: "#3085d6",
                cancelButtonColor: "#d33",
            }).then((result) => {
                if (result.isConfirmed) {
                    location.href = "/Values/Index"; 
                } else {
                    location.href = "/Values/"; 
                }
            });
        },
        error: function (xhr, err) {
            alert("Error: " + err); 
        }
    });
}

//view
Index.html

@model List<EntityProject.Models.ViewName.Cricketers>
@{
    ViewBag.Title = "Index";
}

<link rel="stylesheet" type="text/css" href="https://cdn.datatables.net/2.2.2/css/dataTables.dataTables.min.css">
<script type="text/javascript" src=" https://cdn.datatables.net/2.2.2/js/dataTables.min.js ">  </script>
<script src="~/Scripts/CricketTeam.js"></script>
<h2>Chennai Super Kings</h2>

<button class="btn btn-success" style="float:right" onclick="add();">Add</button>


<table class="table table-striped"  id="Cricket">
    <thead>
        <tr>
            <th>PlayerId </th>
            <th>PlayerName</th>
            <th>Roles</th>
            <th>Batting</th>
            <th>Bowling</th>
            <th>HighestScore</th>
            <th>wickets</th>
            <th>Action</th>
        </tr>
    </thead>
    <tbody>
        @foreach (EntityProject.Models.ViewName.Cricketers item in Model)
        {
            <tr>
                <td>@item.PlayerId</td>
                <td>@item.PlayerName</td>
                <td>@item.Roles</td>
                <td>@item.Batting</td>
                <td>@item.Bowling</td>
                <td>@item.Hightest_score</td>
                <td>@item.wickets</td>

                <td>
                    <button class="btn btn-primary" onclick="Edit(@item.PlayerId);">Edit</button>
                    <button class="btn btn-info" onclick=" View(@item.PlayerId);">View</button>
                    <button class="btn btn-danger" onclick="deletePlayer(@item.PlayerId);">Delete</button>
                </td>
            </tr>
        }

    </tbody>
</table>
<script>
    $(document).ready(function () {
        $("#Cricket").dataTable();
    });
</script>


///Add.html
@{
    ViewBag.Title = "add";
}
<script src="~/Scripts/CricketTeam.js"></script>
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

<h2> Add PlayersName</h2>
<div class="container">

    <div class="form-group">
        <label for="playerName">Player Name</label><br />
        <input type="text" id="playerName" name="playerName" / required>
    </div><br />

    <div class="form-group">
        <label for="roles">Roles</label><br />
        <input type="text" id="roles" name="roles" / required>
    </div><br />

    <div class="form-group">
        <label for="batting">Batting</label><br />
        <input type="text" id="batting" name="batting" />
    </div><br />

    <div class="form-group">
        <label for="bowling">Bowling</label><br />
        <input type="text" id="bowling" name="bowling" / >
    </div><br />

    <div class="form-group">
        <label for="highest">Highest Score</label><br />
        <input type="number" id="highest" name="highest" / required>
    </div><br />

    <div class="form-group">
        <label for="wickets">Wickets</label><br />
        <input type="number" id="wickets" name="wickets" / required>
    </div><br />

    <button class="btn btn-outline-info" type="submit" onclick="clickbutton();">Submit</button>

</div>



//Edit.html

@model EntityProject.Models.Cricket

@{
    ViewBag.Title = "Edit";

}


<h2>Edit</h2>

<script src="~/Scripts/CricketTeam.js"></script>
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

<div>
    <div>
        <label>PlayerId: </label>
        <input type="text" id="PlayerId" value="@Model.PlayerId" disabled />
    </div>
    <br />


    <div>
        <div>
            <label>PlayerName: </label>
            <input type="text" id="PlayerName" value="@Model.PlayerName" />
        </div>
        <br />

        <div>
            <label>Role: </label>
            <input type="text" id="Role" value=" @Model.Roles" />
        </div>
        <br />

        <div>
            <label>Batting: </label>
            <input type="text" id="Batting" value=" @Model.Batting" />
        </div>
        <br />

        <div>
            <label>Bowling: </label>
            <input type="text" id="Bowling" value=" @Model.Bowling" />
        </div>
        <br />

        <div>
            <label>HighestScore: </label>
            <input type="text" id="HighestScore" value=" @Model.Hightest_score" />
        </div>
        <br />

        <div>
            <label>Wickets: </label>
            <input type="text" id="Wickets" value=" @Model.wickets" />
        </div>
        <br />


        <button class="btn btn-success" onclick="clickbutton1();">Save</button>


    </div>
</div>


//View.html


@model EntityProject.Models.Cricket

@{
    ViewBag.Title = "View";
}

<h2>View</h2>

<script src="~/Scripts/CricketTeam.js"></script>
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

<div>
    <div>
        <label>PlayerId: </label>
        <input type="text" id="PlayerId" value="@Model.PlayerId" disabled />
    </div>
    <br />


    <div>
        <div>
            <label>PlayerName: </label>
            <input type="text" id="PlayerName" value="@Model.PlayerName" disabled />
        </div>
        <br />

        <div>
            <label>Role: </label>
            <input type="text" id="Role" value=" @Model.Roles" disabled />
        </div>
        <br />

        <div>
            <label>Batting: </label>
            <input type="text" id="Batting" value=" @Model.Batting" disabled />
        </div>
        <br />

        <div>
            <label>Bowling: </label>
            <input type="text" id="Bowling" value=" @Model.Bowling" disabled />
        </div>
        <br />

        <div>
            <label>HighestScore: </label>
            <input type="text" id="HighestScore" value=" @Model.Hightest_score" disabled />
        </div>
        <br />

        <div>
            <label>Wickets: </label>
            <input type="text" id="Wickets" value=" @Model.wickets" disabled />
        </div>
        <br />


        <button class="btn btn-success" onclick="back();">Back</button>


    </div>
</div>






