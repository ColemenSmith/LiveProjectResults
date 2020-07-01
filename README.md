# LiveProjectResults

## What I have been doing

I have now participated in two live projects at The Tech Academy. In these projects I worked with the Django framework on one, and ASP .Net MVC & Entity Framework on the other. With these projects I got an amazing experience of working within a team environment, and furthering my practices with bug solving and errors in code. This practice with the frameworks has lead me feel a lot more confident in my ability to be able to navigate the code and to just be able to simply read them and understand what is going on. 
 
## Django Live Project
 
In this project, we were tasked with creating an app that harnesses an API to create a live feed of information on whichever subject we chose. During this project, I did a lot of work on designing my pages with the use of Bootstrap and conventional CSS. The project was only a two week sprint and I was working at the time so I didn't get to progress as far as I would have liked to have, but I made the most of the experience as I could have in the time. My understanding of the Django framework is a lot more clear than it was and I understand how it all ties together and works. I didn't get to work on using my API at all so the most of my practice with Django is the fundamentals, and increasing my comfort with Bootstrap. I don't have any interesting code to show because it's pretty simple code, more of my code will be from the MVC project.

## ASP .Net MVC & Entity Framework Live Project

This project was the one that I learned the most from and put the most time into. In this project the first thing I did was lock access to a webpage for only admins. This fix was pretty simple, all I did was filter access by admin users and then I created a page that users get redirected to if they have already logged in and don't have the proper permissions.

This was the simple fix:

    [TheatreAuthorize(Roles = "Admin")]
    
And then I created the TheatreAuthorize method:

    public class TheatreAuthorize : AuthorizeAttribute
    {
        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
        {
            if (!filterContext.HttpContext.User.Identity.IsAuthenticated)
            {
                // if not logged, it will work as normal and redirect to login
                base.HandleUnauthorizedRequest(filterContext);
            }
            else
            {
                //logged and without permissions to access, redirects to custom action
                filterContext.Result = new RedirectToRouteResult(new RouteValueDictionary(new { controller = "Account", action = "UnauthorizedAccess" }));
            }
        }
    }
    
This work concluded my first few stories. After that I began the work on detecting photo duplication into our database. In our project we turned our photos into an array of bytes so that's how I would compare them.

This is how I detected duplication, as well as sending a model error to the user to ask them if they wanted to view the existing photo or edit it:

    bool isDuplicate = db.Photo.Any(p => p.PhotoFile == photo.PhotoFile);
                if (isDuplicate)
                {
                    var photoId =
                        from p in db.Photo
                        where p.PhotoFile == photo.PhotoFile
                        select p.PhotoId;

                    ModelState.AddModelError("PhotoFile", "Image is a duplicate, would you like to <a href=\"Edit/" + photoId.FirstOrDefault() + "\">edit</a> or <a
                    href=\"Details/" + photoId.FirstOrDefault() + "\">view</a> the existing photo?");

                }
                else
                {
                    db.Photo.Add(photo);
                    db.SaveChanges();
                    return RedirectToAction("Index");
                }
                
I then needed to prevent duplication from the edit page, which was a new challenge in itself. For the edit page, I also had to figure out how to let the user just edit information about the photo if they didn't want to upload a new one. I did that this way:

    if(file != null && file.ContentLength > 0)
                {
                    byte[] photoArray = ImageBytes(file);
                    photo.PhotoFile = photoArray;
                    Bitmap img = new Bitmap(file.InputStream);
                    photo.OriginalHeight = img.Height;
                    photo.OriginalWidth = img.Width;

                    bool isDuplicate = db.Photo.Any(p => p.PhotoFile == photo.PhotoFile);
                    if (isDuplicate)
                    {
                        var photoId =
                            from p in db.Photo
                            where p.PhotoFile == photo.PhotoFile
                            select p.PhotoId;

                        ModelState.AddModelError("PhotoFile", "Image is a duplicate, would you like to <a href=\"/Photo/Details/" + photoId.FirstOrDefault()
                        + "\">view</a> the existing photo?");
                    }
                    else
                    {
                        db.SaveChanges();
                        return RedirectToAction("Index");
                    }
                }
                else
                {
                    var photoFile =
                        from p in db.Photo
                        where p.PhotoId == photo.PhotoId
                        select p.PhotoFile;
                    photo.PhotoFile = photoFile.FirstOrDefault();
                    db.Entry(photo).State = EntityState.Modified;
                    db.SaveChanges();
                    return RedirectToAction("Index");
                }
                
This is as much as I did on my live projects. Over all it was a very enlightening experience and I learned so much from the whole thing. I learned how to be a good team member, and how to better utilize the information online. 
