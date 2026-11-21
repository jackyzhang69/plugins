# AnyWeb — what changed in this version

Version `0.2.29` fills a supported Express Entry profile up to the last review before submission. It can also fill and check supported BC PNP registration fields before Register, without saving or creating an account. Neither task submits the form. Your system may ask you to confirm this build.

- Filling an Express Entry profile takes about half the time: each step now continues as soon as the website has finished responding, instead of after a fixed pause.
- A slow website is waited for. The run stops only when the website has not answered for several minutes, and says so.
- The page in the AnyWeb Chrome window follows the window when you resize it.
- When an applicant's situation is not covered yet, the result lists every affected question before Chrome opens. Where it stopped and why is reported automatically, without the applicant's answers; you can add detail with Tell Jacky.

Email codes still pause so you can give them to this assistant, and a step that cannot run is reported as not done; feedback counts as delivered only when the service confirms receipt, and an available repair is not permission to submit.
