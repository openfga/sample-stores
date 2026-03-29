# OpenFGA for Applicant Tracking Systems

## Use Case

This model represents the authorization needs of an **Applicant Tracking System (ATS)**, like Greenhouse, Lever, or Workable. These platforms manage job postings, candidate pipelines, interviews, scorecards, and hiring workflows.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own jobs, candidates, and recruiting pipelines.
- **Recruiting roles**: Organization-level roles (`admin`, `recruiter`, `hiring_manager`) control access to the hiring pipeline. Admins have full access, recruiters manage candidates and applications, and hiring managers own specific jobs.
- **Job-scoped access**: Hiring managers and recruiters are assigned per job, controlling who can view applications, change interview stages, and close positions.
- **Candidate privacy**: Candidate profiles can be viewed and edited by organization recruiters and admins. Hiring managers do not directly access candidate profiles; they only access candidates through job-linked applications.
- **Interview workflow**: Organizers manage scheduled interviews, while interviewers can only view interviews in which they participate.
- **Scorecard confidentiality**: Scorecards are visible to the interviewer, department heads in the application's hiring chain, and organization admins. Other interviewers cannot see each other's feedback.
- **Offer management**: Offer creators can view and edit offers, but only admins can approve them, ensuring proper authorization in the hiring decision chain.
- **Department & office structure**: Departments and offices are viewable by all organization members, providing organizational context for job listings.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `applicant-tracking-system` directory, run `fga model test --tests store.fga.yaml`
