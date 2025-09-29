Containers a ephemeral in nature they can goes down easly.
In case if container dies either it recreated manually or we need to create some scripts that create it again---> That is very hard or error pron stuff.
Login(ssh)--->system(look into it)--->recreate container(need to do it every time container dies),24/7,everytime support,can't so it for multiple container in enterprise application,No HA, rollout issue, Exposing application can be issue(manually),networking, LB, service discovery,we need to take care of all of this thing manually, good for small application,use docker compose,use VPS,
So K8S resolve this issue it is a orchestrator that can run, manage and scale a containerize application.
K8S is not always a solution.

