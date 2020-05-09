#ChronJobs
kubectl run --schedule="*/3 * * * *" --restart=OnFailure --image=alpine sleep 10