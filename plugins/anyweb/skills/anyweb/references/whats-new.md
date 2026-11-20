# AnyWeb — what changed in this version

Version `0.2.28` fills a supported Express Entry profile up to the last review before submission. It can also fill and check supported BC PNP registration fields before Register, without saving or creating an account. Neither task submits the form. Your system may ask you to confirm this build.

- Language scores now fill for every supported test type, in the test the applicant actually took.
- Optional questions on the route are asked too, and "not in my files" is accepted as an answer and leaves that field empty.
- One applicant's answers are never reused for another applicant.
- Answers that lead to the website's not-eligible page stop before Chrome opens.
- Repeated entries such as jobs, schools and family members match the pack's schema when you resume after a question.
- Question labels are clearer, including which person a question is about, such as the spouse or partner.

Email codes still pause so you can give them to this assistant, and a step that cannot run is reported as not done; feedback counts as delivered only when the service confirms receipt, and an available repair is not permission to submit.
