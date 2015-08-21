---
layout: page
title: Release Notes
permalink: /release-notes/
---

Look here for recent updates to the Lesson.ly platform.

### Coming soon

- More good stuff!

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
