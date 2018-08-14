# Cuirass Web interface
## GSoC 2018 report 
---

  For the last three months I have been working with the Guix team as a
  Google Summer of Code intern. The title of my project is "GNU Guix
  (Cuirass): Adding a web interface similar to the Hydra web interface".

  Cuirass is a continuous integration system which monitors the Guix git
  repository, schedules builds of Guix packages, and presents the build
  status of all Guix packages.  Before my project, Cuirass did not have
  a web interface. The goal of the project was to implement an interface
  for Cuirass which would allow a user to view the overall build
  progress, details about evaluations, build failures, etc.  The web
  interface of [Hydra](https://hydra.nixos.org/) is a good example of
  such a tool.

  In this post, I present a final report on the project. The Cuirass
  repository with the changes made during the project is located at
  [https://git.savannah.gnu.org/cgit/guix/guix-cuirass.git](https://git.savannah.gnu.org/cgit/guix/guix-cuirass.git). A
  working instance of the implemented interface is available at
  [https://berlin.guixsd.org/](https://berlin.guixsd.org/).  You can
  find more examples and demonstrations of the achieved results below.

  # About Cuirass

  Cuirass is designed to monitor git repositories containing Guix
  package definitions and build binaries from these package definitions.
  The state of planned builds is stored in a SQLite database. The key
  concepts of the Cuirass internal state are:

  - **Job specification**. Specifications state what has actually to be
    done by Cuirass. A specification [is
    defined](https://www.gnu.org/software/guix/manual/en/html_node/Continuous-Integration.html)
    by a Scheme data structure (an association list) which includes a
    specification name, repository URLs, as well as their branches and a procedure
    `proc` that specifies how this is to be built.

  - **Evaluation**. An evaluation is a high-level build action
    related to a certain revision of a repository of a given
    specification.  For each specification, Cuirass continuously
    produces new evaluations which build different versions of the
    project represented by revisions of corresponding repositories.
    Derivations and builds (see below) each belong to a specific
    evaluation.

  - **Derivation**. Derivations represent low-level build actions.  They
    store such information as name of a build script and its arguments,
    input and output of a build action, target system type, and
    necessary environment variables.

  - **Build**. A build is a result of build actions that are prescribed
    by a derivation.  This could be a failed build or a directory
    containing the files that were generated by compiling a package.

  Besides the core which executes build actions and records their
  results in the database, Cuirass includes a web server which
  previously only responded to a handful of API requests with JSON
  containing information about the current status of builds.

  # Web interface

  The Cuirass web interface implemented during the project is served by
  the Cuirass web server whose functionality has been extended to
  generating HTML responses and serving static files.  General features
  of the interface are listed below.

  - The backend is written in Guile and implements request processing
    procedures which parse request parameters and extract specific data
    to be displayed from the database.

  - The frontend consists of HTML templates represented with [Guile
    SXML](https://www.gnu.org/software/guile/manual/html_node/SXML.html)
    and the [Bootstrap 4](https://getbootstrap.com/) CSS library.

  - The appearance is minimalistic. Every page includes only specific
    content information and basic navigation tools.

  - The interface is lightweight and widely accessible.  It does not use
    JavaScript which makes it available to users who do not want to have
    JavaScript running in the browser.

  ## Structure

  Let's review the structure of the interface and take a look at the
  information you can find in it.  Note that the web interface
  screenshots presented below were obtained with synthetic data loaded
  into Cuirass database.

  ### Main page

  The main page is accessible on the root request endpoint (`/`). It displays a list of all the specifications stored in the
  Cuirass database.  Each entry of the list is a clickable link which
  leads to a page about the evaluations of the corresponding
  specification (see below).

  Here is an example view of the main page.

  ![Main page screenshot](https://github.com/TSholokhova/gsoc-2018-report/blob/master/img/cuirass-wi-main.png)

  ### Evaluations list

  The evaluations list of a given specification with name `<name>` is
  located at `/jobset/<name>/`.  On this page, you can see a list of
  evaluations of the given specification starting from the most recent ones.
  You can navigate to older evaluations using the pagination buttons at
  the bottom of the page.  In the table, you can find the following
  information:

  - The ID of the evaluation which is clickable and leads to a page with
    information about all builds of the evaluation (see below).

  - List of commits corresponding to the evaluation.

  - Build summary of the evaluation: number of succeeded (green), failed
    (red), and scheduled (grey) builds of this evaluation.  You can open
    the list of builds with a certain status by clicking on one of these
    three links.

  Here is a possible view of the evaluations list page:

  ![Screenshoot of evaluations list](https://github.com/TSholokhova/gsoc-2018-report/blob/master/img/cuirass-wi-evals.png)

  ### Builds list

  The builds list of a evaluation with ID `<id>` is located at
  `/eval/<id>/`.  On this page, you can see a list of builds of the
  given evaluation ordered by their stop time starting from the most
  recent one.  Similarly to the evaluation list, there are pagination
  buttons located at the bottom of the page.  For each build in the
  list, there is information about the build status (succeeded, failed,
  or scheduled), stop time, nixname (name of the derivation), system, and
  also a link to the corresponding build log.  As said above, it is
  possible to filter builds with a certain status by clicking on the
  status link in the evaluations list.

  ![Screenshot of builds list](https://github.com/TSholokhova/gsoc-2018-report/blob/master/img/cuirass-wi-builds.png)

  # Summary

  Cuirass now has the web interface which makes it possible for users to
  get an overview on the status of Guix package builds in a
  user-friendly way.  As the result of my GSoC internship, the core of
  the web interface was developed.  Now there are several possibilities
  for future improvements and I would like to welcome everyone to
  contribute.

  It was a pleasure for me to work with the Guix team.  I would like to
  thank you all for this great experience!  Special thanks to my GSoC
  mentors: Ricardo Wurmus, Ludovic Courtès, and Gábor Boskovits, and
  also to Clément Lassieur and Danny Milosavljevic for their guidance
  and help throughout the project.
