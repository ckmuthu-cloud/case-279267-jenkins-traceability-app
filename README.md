# jenkins-traceability-poc-app

Demo app used to validate a build-to-asset traceability architecture for
CloudBees CI / Jenkins (GitHub variant of the GitLab-based design discussed
in Zendesk case #279267).

The Jenkinsfile here:
1. Builds a dummy artifact and computes its checksum (stand-in for an image
   digest / Lambda code hash / artifact checksum).
2. Calls `stampProvenance()` from the global shared library
   `jenkins-traceability-poc-sharedlib`, which fingerprints a file whose
   content is exactly the digest string, so the digest itself becomes a
   stable lookup key in Jenkins' fingerprint database, and archives a
   `provenance.json` with commit/repo/build details.

Goal: given only the digest (as Wiz/ArmorCode would report it), resolve the
originating GitHub repo, commit, and Jenkins build in one lookup.
