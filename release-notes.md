---
layout: page
title: Release Notes
permalink: /release-notes/
---

Look here for recent updates to the Lesson.ly platform.

### Coming Soon

- We fixed a bug that caused copied lessons to copy their "Last Edited" value to the new lesson, even when that date was in the past.
- More good stuff!

### Wednesday, March 23, 2016, 4:35 PM EDT

- The Learning Library: a new experience for elective training material! Make training content available on demand for your learners. Read more at our website. http://www.lesson.ly/blog/introducing-the-learning-library/

### Wednesday, March 23, 2016, 12:15 AM EDT

- New Dashboard Design: We redesigned the Learners' dashboard for a better look and feel, to make it easier to find content, and for a smoother overall experience.

### Thursday, March 17, 2016, 3:19 PM EDT

- For customers using the Learning Library, you can now filter and sort lessons and courses by those most recently added to the Library, so it’s easier than ever to see what’s new to learn!
- We’ve made big improvements to searching on the Dashboard: not only is it faster, but by popular request you can now search course assignments by content in any of the courses’ lessons.

### Friday, March 11, 2016, 1:39 PM EST

- An update to the API which allows for finding users by `email` or `ext_uid`. See [our API docs](http://docs.lesson.ly/#list-users) for more info.
- We’ve made lesson overview pages faster, especially for lessons with lots of quiz questions.

### Wednesday, March 2, 2016, 9:25 AM EST

- Improved performance of the Lesson Overview page

### Monday, February 29, 2016, 2:20 PM EST

- Fixed a bug where Free Response questions had overlapping checkboxes for Required and Graded options in the editor.
- Groups can now be created and deleted from the API.

### Thursday, February 25, 2016, 4:18 PM EST

- By popular request, we’re now displaying users’ events feeds by year so it's easier to tell when things happened.
- An update to our WYSIWYG text editor fixing a number of small issues.
- We’ve fixed a bug where lessons and courses were still appearing with a yellow border on the dashboard even after they’d been graded, instead of the expected green border.

### Wednesday, February 24, 2016, 3:00 PM EST

- Fixed a bug where changing a lesson's title might not display the updated value the next time a User visited their dashboard.
- In users’ event feeds, we’ll now show events by year, making it easier to tell *which* “Feb 24” they completed an assignment, for example.

### Thursday, February 11, 2016, 3:30 PM EST

- Fixed a bug where creating a user and assigning a lesson set to be auto-assigned caused an error.
- Fixed a bug where users of deactivated accounts would occasionally receive a daily digest email.

### Tuesday, February 9, 2016, 3:49 PM EST

- API support for [listing assignments on a single user](http://docs.lesson.ly/#user-assignments) and [assigning lessons and courses on a single user](http://docs.lesson.ly/#create-user-assignments).
- New lesson description field is now available which you may set via the lesson overview page. Currently, lesson descriptions will show up in assignment notification emails, learning library tiles, and PDF exports of lessons.
- Cross-domain or cross-origin AJAX requests against our API are now supported.

### Tuesday, February 2, 2016, 6:03 PM EST

- We've fixed a bug where Scheduled Assignment dates were not using time zone to display date..
- We've fixed a display bug on overview pages with really long group names.

### Monday, February 1, 2016, 2:10 PM EST

- We've fixed a bug where automatically-assigned lessons were not being assigned to users when logging in for the first time via Single Sign-On.
- We've fixed a bug where group overview pages sometimes wouldn't be updated with the latest completion data from the group's users.
- Some general speed improvements—more to come!

### Thursday, January 14, 2016, 11:40 PM EST

- Scheduled assignments is here! This update allows admins and managers to schedule lessons and courses for assigning to groups or users at a future date. Scheduling an assignment is quite similar to assigning a normal assignment except it will of course require your future assign on date. You may optionally set a future due date or a reassign on recurrence, so you can schedule an assignment once and never fuss with it again. You may view and edit existing scheduled assignments from the Scheduled Assignments Queue.

### Wednesday, January 13, 2016, 12:55 PM EST

- Redesigned lesson, course, user, and group overview pages! These updates bring a fresh new design and some additional functionality to make managing things quicker and easier. See [our blog post for the full run-down](http://www.lesson.ly/blog/overhauling-overview-pages/) of updates!

### Tuesday, January 12, 2016, 5:57 PM EST

- You've always been able to assign custom fields for your users to gather additional info when they sign up. Now, we've added an experimental feature for changing that open-ended text box to a drop-down with predefined options. Only want users to select from a list of, for example, existing department names? Contact <support@lesson.ly> if you’d be interested in using this feature.

### January 11, 2016, 11:06 AM EST

- We've fixed a bug where a user could not be created with an external user id that had already been used, even if the first user had been deleted.

### Wednesday, December 30, 2015, 2:38 PM EST

- We’ve fixed a bug where customers with a custom domain (e.g. `learn.mycompany.com`) configured had their logo in emails linking to their Lesson.ly subdomain (e.g. `mycompany.lesson.ly`).

### Tuesday, December 22, 2015, 11:48 AM EST

- We’ve fixed a bug when importing users where double-clicking the submit button could allow some duplicate users to be created.

### Thursday, December 18, 2015, 11:08 AM EST

- We changed the URLs for taking lessons, but old bookmarks will still work.
- We added the lesson's retake score to both the webhook and API response.

### Friday, December 11, 2015, 11:28 AM EST

- There was a bug that caused manager users to lose their managed groups when that manager was updated by an admin. No data was lost, but managers who were edited since the last update will need to be assigned to their groups again. This release fixes that bug.

### Wednesday, December 9, 2015, 4:09 PM EST

- Updating the minimum retake score for lessons just got much faster.
- When creating and editing users via our [API](http://docs.lesson.ly/), you can now specify an `ext_uid` for the user. This is typically an identifier for a user in another system, and can help with syncing data and integrations.
- For security, we now limit the number of failed login attempts for your account.
- We added an experimental feature to allow managers to create the groups (which they then manage). Interested in trying this out? Contact <support@lesson.ly> to have it enabled for your account.

### Thursday, December 3, 2015, 5:08 PM EST

- We removed the image search feature from the lesson editor, as the Google API it relied on was discontinued. We are considering options for updating this feature in the future.

### Tuesday, December 1, 2015, 1:43 PM EST

- We fixed a bug that reported an incorrect number of responses to free response questions on lesson overview pages.
- We've improved performance while taking an completing lessons.

### Monday, November 30, 2015, 12:33 PM EST

- We fixed a bug where some emails incorrectly had the Lesson.ly logo at the bottom when it should have been your company's logo.
- We've added a new feature which allows managers to add and edit users within the groups that they manage. Contact <support@lesson.ly> to have this feature enabled for your account.

### Thursday, November 19, 2015, 1:25 PM EST

- With apologies for some recent slowness in the app, we've made a performance improvement which should help keep things humming along during periods of especially high traffic.

### Monday, November 16, 2015, 2:04 PM EST

- We fixed an error when a manager or creator would attempt to filter their lessons by tag.

### Friday, October 23, 2015, 9:30 AM EDT

- We made added some database indexes which should help speed up parts of the application
- We added the ability to select and deselect all assignments when adding a user to a group with assignments
- We fixed a usability issues where the due date couldn't be cleared from an assignment after being set when adding a user to a group with assignments
- The "Assign" modal now keeps its state until it is explicitly canceled
- We modified the email sent to new users to allow for direct SSO login via the email

### Friday, October 16, 2015, 1:45 PM EDT

- We added the ability to hide username/password login for SSO users
- We fixed a bug where sometimes deactivated users would still get assignment reminder emails
- We added a new feature to allow the grader to provide feedback when grading free response answers

### Thursday, October 15, 2015, 12:00 PM EDT

- We fixed a bug where trying to take a lesson with no content would cause an error
- We added `ext_uid` to certain API responses where users are listed
- We updated the 404 page to be potentially less offensive
- We improved the security for anonymous login

- Nothing yet!

### Thursday, October 8, 2015, 12:14 PM EDT

- We added a new feature - Smart Groups. In this release, there will only be one Smart Group: "Everyone". More will come in due time.
- We fixed a bug that caused an error when deleting the retake score of a lesson.
- We improved the database performance relating to several lesson-related pages, most notably completion of an assignment.

### Monday, October 5, 2015, 4:58 PM EDT

- We fixed a bug where the gradebook was showing scores for lessons that were previously taken, but had been unassigned.
- We added a feature that assigns a useful lesson to the new user upon starting a new trial.

### Tuesday, September 29, 2015, 11:59 AM EDT

- You can now filter Gradebook results by assignments completed in the past week, month, quarter, or a custom date range.

### Monday, September 28, 2015, 2:19 PM EDT

- We fixed a bug where exporting a single user's Gradebook data would come up empty.
- We fixed a bug where sometimes report cards would be out of order and have incorrect scores.

### Tuesday, September 22, 2015, 11:05 AM EDT

- We've updated the default Lesson.ly app logo to work with the new header style.
- We fixed an error which would occur when trying to answer questions where the lesson's creator had left the question text blank.

### Monday, September 21, 2015, 4:04 PM EDT

- We've redesigned the Gradebook! New features include easier selection of groups, lessons, and courses; the ability to hide the filters and view the Gradebook data full-screen; and a number of performance improvements.

### Monday, September 14, 2015, 10:35 AM EDT

- We fixed an issue where Report Card pages would get cut off when answers contain really long URLs.
- We fixed an edge case where certain assignments you could see in the Gradebook would not appear in the exported CSV file.
- We fixed a bug where a user would show up twice in the gradebook when in two selected groups.
- We fixed a bug where new creator, manager, and admin users created via bulk user import would not get an invitation email.
- We fixed an issue where multiple select and graded free response questions were not included in gradebook average scores.

### Wednesday, September 9, 2015, 11:11 AM EDT

- We've improved the performance of the Gradebook page, especially when viewing courses.

### Friday, August 28, 2015, 10:15 AM EDT

- We fixed an error that occurred when double-clicking the “Create My Account” button.
- We fixed a bug on course overview pages where the number of complete/incomplete assignments was limited to 100 even if more assignments were in that state.

### Friday, August 21, 2015, 4:23 PM EDT

- On lesson overview pages, the number of “Completed” and “Incomplete” assignments now includes lessons assigned via courses as well as those that were assigned directly. Clicking one of those links now shows how the lesson was assigned.
- We fixed an error when fetching information about some users via the API.

### Thursday, August 20, 2015, 2:43 PM EDT

- We fixed a bug where sometimes a learner could be assigned the same lesson twice, causing the lesson to appear twice on their dashboard.

### Tuesday, August 18, 2015, 4:12 PM EDT

- Assignment emails now included a button directly in the subject line (on supported clients like Gmail and Yahoo) to take you directly to the lesson, course, or dashboard.
- Lessons can now each have their own minimum score below which learners will be automatically reassigned. This defaults to the retake score on the Company Settings page if one is set.
- When viewing lists of assignments, they can now be selected in order to reassign, unassign, or change the due dates of more than one at a time.

### Tuesday, August 11, 1:41 PM EDT

- We fixed a bug where CSV files with certain newline formats wouldn't work when importing users.
- New feature: after creating an assignment, you will see a new dialog to confirm that the assignment was successful.

### Friday, August 7, 2015, 1:51 PM EDT

- We fixed a bug when reassigning where after choosing a due date, the due date would appear hidden until you clicked "Reassign".
- We fixed a bug which showed an error when attempting to grade lessons containing multiple-select questions.
- We fixed an issue with Bulk User Import when uploading a CSV with CRLF-style line breaks.

### Wednesday, August 5, 2015, 11:23 PM EDT

- We fixed an issue where if you exited the Assign modal on a user's, group's, lesson's, or course's page when you re-open it's still populated.
- New feature: bulk create and update users with a CSV file!

### Friday, July 31, 2015, 1:22 PM EDT

- We fixed an issue where more than just free response questions could be marked as "Graded". Currently, only free response questions can be graded manually. Multiple choice and multiple select are graded automatically, and surveys should not be graded.
- We fixed an error which sometimes occurred when visiting Lesson.ly with too many subdomains in the URL, e.g. `www.mycompany.lesson.ly`. This now redirects to `mycompany.lesson.ly`.
- When copying a lesson, you'll now be made an owner of the lesson. This fixes an issue where a user could copy a lesson but then not have permission to view the copy.

### Wednesday, July 29, 2015, 2:11 PM EDT

- Graded free response questions: a new feature! When adding free response questions to a lesson, you can now mark them as “Graded”, requiring admins and managers to mark responses as either correct or incorrect in order to provide learners with feedback. Graded free response questions also contribute to a learner's score for a lesson. Learn more [on our blog](http://www.lesson.ly/blog/gradable-free-response/).

### Monday, July 27, 2015, 12:01 PM EDT

- We fixed a bug when assigning multiple lessons through the Assign dialog.

### Monday, July 27, 2015, 10:45 AM EDT

- We fixed a bug where you could accidentally reassign a lesson to a user via the Assign dialog.
- New feature: when on a user's, group's, lesson's, or course's page, the Assign dialog will automatically fill that field in when opened!
- New feature: when on a user's overview page, there is a new button that allows you to send that user an email to sign up or reset their password!

### Friday, July 24, 2015, 3:14 PM EDT

- We fixed a bug where viewing free response question details would sometimes result in an error.
- We added Lesson Performance metrics for survey and multiple select questions.
- We improved the performance for calculating Lesson Performance metrics.
- We improved the Assign modal on Lesson/Course/Group/User pages so that they auto populate

### Thursday, July 23, 2015, 3:36 PM EDT

- We'll no longer retry attempts to deliver overdue assignment reminders if you've completed the assignment since we last tried.
- We fixed a bug where deleting a user via the API failed to also delete their assignments.

### Thursday, July 16, 2015, 11:09 AM EDT

- New questions will now be required by default.
- The course API endpoint now returns an array of lessons instead of a JSON string.

### Monday, July 13, 2015, 11:11 AM EDT

- Update the lesson editor plugin to fix some bugs

### Monday, July 6, 2015, 9:41 AM EDT

- API V1 release
- Fixed a bug that caused an assignment's latest activity not to update in some circumstances

### Monday, June 29, 2015, 11:30 AM EDT

- Add Survey questions to ask multiple choice questions that should not be graded
- Fixed a bug with providing custom fields when signing up to take a lesson

### Wednesday, June 24, 2015, 9:40 AM EDT

- Users can now opt out of receiving daily digest emails through the user settings page.
- Add the ability to export lessons as a PDF

### Friday, June 19, 2015, 3:52 PM EDT

- Multiple Select Questions now possible, for Multiple Choice Questions with more than one answer.
- Questions can now be marked as "Required". Learners will not be able to complete a lesson without answering all required questions.

### Thursday, June 11, 2015, 1:30 PM EDT

- All questions are now on the report card, in order, regardless of whether or not they were actually answered, and now in the correct order.
- Upgraded custom report card header editor for formatting options.
- Disabled changing a question's type after creating the question to avoid confusion and complexity.
- Bug fixes.

### Monday, June 8, 2015, 2:22 PM EDT

- When building lessons, you can now select which question type to create (e.g. Multiple Choice, Free Response). More coming soon!
- Fixed a bug where searching lessons sometimes didn't reset pagination

### Friday, June 5, 2015, 2:50 PM EDT

- Updated simpler registration process
- Fixed a bug in the lesson editor where lines ending with ".jpg" or ".gif" would mysteriously disappear
- When a learner fails a lesson, they are shown the questions they got wrong so that they may focus on those when they retake the lesson
- Fixed a bug that made the actual title of an untitled lesson "Untitled Lesson"
- Fixed a bug in the webhook that stripped query strings from the webhook URL
- Fixed a bug in the webhook that did not count any response status codes besides 200 as successful
- All creators and managers can now see aggregate answer data for lessons they have ownership access to; individual responses are still visible only to those users' managers and admins

### Wednesday, May 27, 2015, 9:04 AM EDT

- Updated gradebook CSV export to include time for completion date column
- Auto Assignment for Lessons and Courses for current users, new users, and anonymous users

### Wednesday, May 20, 2015, 11:26 AM EDT

- Fixed bug where due date was incorrectly changed based on user's time zone
- Added preliminary API

### Tuesday, May 19, 2015, 11:10 AM EDT

- External IDs (e.g. `ext_uid` values) used for anonymous login may now contain hyphens and underscores in addition to alphanumeric characters
- Aggregate data for multiple choice questions now takes into account all attempts by each learner

### Tuesday, May 12, 2015, 1:15 PM EDT

- New, easier to use table layout for assignments
- Various bug fixes

### Wednesday, April 29, 2015, 3:30PM EDT

- New Feature that now allows Admins to specify a custom branding color ([see blog post](http://www.lesson.ly/blog/the-colors-announcing-enhanced-branding)).
- If a learner is assigned a course where they've already completed all of the lessons, they will now be required to retake them.

### Tuesday, April 21, 2015, 12:13PM EDT

- Fixes a bug which caused the browser to slow down when answering many questions on a single page.
- Fixes a bug where some unassigned lessons continued to appear in the Gradebook.

### Monday, April 20, 2015, 1:55PM EDT

- Bug fixes

### Friday, April 17, 2015, 5:15PM EDT

- Localized time - Completed time on report cards now shown in current time-zone.
- See all answers to free response questions by clicking the link on the Lesson Overview page

### Thursday, April 16, 2015, 12:58PM EDT

- Fixes a bug with triggers where a trigger would not be deleted when any of its releated users, groups, lessons, or courses were deleted

### Wednesday, April 15, 2015, 4:38PM EDT

- Fixed a bug that made it appear that previously answered questions were blank

### Tuesday, April 14, 2015, 5:09PM EDT

- Enable lesson copying directly to another company
- Admin interface updates

### Tuesday, April 14, 2015, 10:00AM EST

- Added triggers feature to enable automatic assignment upon completion of another assignment
- Aggregate multiple choice answer data on lesson overview page
- Fixed a bug that caused the back button on the edit lesson page to result in an error
- Soft delete users to enable deleted user restoration

### Monday, April 13, 2015, 9:40AM EDT

- Added configurability for SAML responses
