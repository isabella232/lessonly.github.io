---
layout: page
title: Release Notes
permalink: /release-notes/
---

Look here for recent updates to the Lesson.ly platform.

### Coming Soon

- Users can now opt out of receiving daily digest emails through the user settings page.

### Friday, June 19, 2015, 3:52 PM EST

- Multiple Select Questions now possible, for Mulitple Choice Questions with more than one answer. 
- Questions can now be marked as "Required". Learners will not be able to complete a lesson without answering all required questions.

### Thursday, June 11, 2015, 1:30 PM EST

- All questions are now on the report card, in order, regardless of whether or not they were actually answered, and now in the correct order.
- Upgraded custom report card header editor for formatting options.
- Disabled changing a question's type after creating the question to avoid confusion and complexity.
- Bug fixes.

### Monday, June 8, 2015, 2:22 PM EST

- When building lessons, you can now select which question type to create (e.g. Multiple Choice, Free Response). More coming soon!
- Fixed a bug where searching lessons sometimes didn't reset pagination

### Friday, June 5, 2015, 2:50 PM EST

- Updated simpler registration process
- Fixed a bug in the lesson editor where lines ending with ".jpg" or ".gif" would mysteriously disappear
- When a learner fails a lesson, they are shown the questions they got wrong so that they may focus on those when they retake the lesson
- Fixed a bug that made the actual title of an untitled lesson "Untitled Lesson"
- Fixed a bug in the webhook that stripped query strings from the webhook URL
- Fixed a bug in the webhook that did not count any response status codes besides 200 as successful
- All creators and managers can now see aggregate answer data for lessons they have ownership access to; individual responses are still visible only to those users' managers and admins

### Wednesday, May 27, 2015, 9:04 AM EST

- Updated gradebook CSV export to include time for completion date column
- Auto Assignment for Lessons and Courses for current users, new users, and anonymous users 

### Wednesday, May 20, 2015, 11:26 AM EST

- Fixed bug where due date was incorrectly changed based on user's time zone
- Added preliminary API

### Tuesday, May 19, 2015, 11:10 AM EST

- External IDs (e.g. `ext_uid` values) used for anonymous login may now contain hyphens and underscores in addition to alphanumeric characters
- Aggregate data for multiple choice questions now takes into account all attempts by each learner

### Tuesday, May 12, 2015, 1:15 PM EST

- New, easier to use table layout for assignments
- Various bug fixes

### Wednesday, April 29, 2015, 3:30PM EST

- New Feature that now allows Admins to specify a custom branding color ([see blog post](http://www.lesson.ly/blog/the-colors-announcing-enhanced-branding)).
- If a learner is assigned a course where they've already completed all of the lessons, they will now be required to retake them.

### Tuesday, April 21, 2015, 12:13PM EST

- Fixes a bug which caused the browser to slow down when answering many questions on a single page.
- Fixes a bug where some unassigned lessons continued to appear in the Gradebook. 

### Monday, April 20, 2015, 1:55PM EST

- Bug fixes

### Friday, April 17, 2015, 5:15PM EST

- Localized time - Completed time on report cards now shown in current time-zone.
- See all answers to free response questions by clicking the link on the Lesson Overview page

### Thursday, April 16, 2015, 12:58PM EST

- Fixes a bug with triggers where a trigger would not be deleted when any of its releated users, groups, lessons, or courses were deleted

### Wednesday, April 15, 2015, 4:38PM EST

- Fixed a bug that made it appear that previously answered questions were blank

### Tuesday, April 14, 2015, 5:09PM EST

- Enable lesson copying directly to another company
- Admin interface updates

### Tuesday, April 14, 2015, 10:00AM EST

- Added triggers feature to enable automatic assignment upon completion of another assignment
- Aggregate multiple choice answer data on lesson overview page
- Fixed a bug that caused the back button on the edit lesson page to result in an error
- Soft delete users to enable deleted user restoration

### Monday, April 13, 2015, 9:40AM EST

- Added configurability for SAML responses
