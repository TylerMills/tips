# Use Case
- Using Proxmox & Containers
- Have (or want) a ZFS pool
- Want to reuse the same ZFS storage between containers
- Don’t want the overhead of running TrueNAS, Unraid, or an NFS server

# 3 Simple Steps
## Create a ZFS pool (if you haven't already)
1. Go to Server (e.g. proxmox)
2. Click Disks (side pane)
3. Click ZFS (side pane)
4. Click Create ZFS (button near top)
5. Name your pool, e.g. `shared-storage`, selecting disks to include in the pool, and save

## Create datasets within ZFS pool
1. Now you need to create datasets within the pool for organization
2. Go to `Datacenter`
3. Click `Storage` (in side pane)
4. Click `Add` -> `ZFS` (near top)
5. Name your dataset, e.g. `movies`, selecting the pool it should be inside, e.g. `shared-storage`
6. Enable thin provisioning to optimize space
7. Click `Save`
8. This dataset now exists at `shared-storage/movies` (or whatever you chose)

## Tell the containers where to find the dataset and how to mount it:
> Don't be intimidated by this, we simply have to edit some text files because the `mount` UI in proxmox creates logical volumes per container, meaning no sharing.
1. Change to the directory where the containers store their setup
   - `cd /etc/pve/lxc`
2. Open the config file for one of your containers (replace `{{container-id}}` below with the container you want to see this storage
   - `nano {{container-id}}.conf`
3. Add the following line, which says `/mnt/movies` in my container should map to `shared-storage/movies`
   - `mp0: /mnt/movies,mp=/shared-storage/movies`
   - Replace /mnt/movies with whatever you want it to be inside the container (e.g. you could do `media/movies`)
   - Replace storage/movies with your `pool/dataset` names configured above
4. Ctrl+X to Save
5. Repeat for every container you want to see this storage
6. Reboot containers
