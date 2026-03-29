# OpenFGA for Learning Management Systems

## Use Case

This model represents the authorization needs of a **Learning Management System (LMS)**, like Canvas, Moodle, or Blackboard. These platforms manage courses, classes, content, collections, and learning activities across educational organizations.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own courses, classes, and learning materials.
- **Educational roles**: Organization-level roles (`admin`, `instructor`, `student`) control access. Admins have full access, instructors can manage courses and grade work, and students can view enrolled content and submit assignments.
- **Course management**: Course owners can edit and publish courses. Enrolled instructors can edit course content. Students enrolled in a course can view it but cannot modify it.
- **Class enrollment**: Instructors and admins can enroll students in class sections. Students can view classes they are enrolled in but cannot modify class settings.
- **Content authorship**: Content authors can edit and delete their own materials. Instructors have broader edit access across the organization.
- **Activity grading workflow**: Instructors and activity creators can grade assignments. Only assigned students can submit work, preventing unauthorized submissions.
- **Course publishing**: Only course owners and admins can publish courses, ensuring curriculum review before content goes live.
- **Collection management**: Collection owners and instructors can organize learning materials. Only admins can delete collections.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `lms` directory, run `fga model test --tests store.fga.yaml`
