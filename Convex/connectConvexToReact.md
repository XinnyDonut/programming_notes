1. ```api``` is auto gennerated, Convex generate the ```api``` obj based on the backend functions in ```/convex``` folder. It includes all the mutaions and queries in the backend. Though ```internalMutation``` and ```internalQuery```can only be accessed in convex backend.
    ```
      import { useState } from 'react';
      import { useMutation } from "convex/react";
      import { api } from "../convex/_generated/api";

      const initWorld = useMutation(api.init);
    ```
2. ```useMutation```is ReactHook from convex that let you call mutation function.Here,```initWorld``` is the function we call inside React to trigger the mutaion function that is called init in the convex backend.For example, here, we are using react state hook tempSelectedMap to call init in the convex backend to init the world.
    ```
    const handleSave= async () => {
        if(activeSection==="map") {
          try{
            await resetWorld()
            await initWorld({
              numAgents:undefined,
              mapId:tempSelectedMap
            });
            setSelectedMap(tempSelectedMap)
            alert("Save successful: Map selection updated")
            setHasChanges(false)
            console.log(selectedMap)
          }catch(error){
            console.error("Failed to save map selection", error)
            alert('Failed to save map' + error)
          }
        }
      }
      ```
3. How Convex Client acts like the service layer

    With a traditional service layer, for example:
    ```
    // apiService.js
    export async function getUsers() {
      const res = await fetch("/api/getUsers");
      return res.json();
    }
    //App.js
    useEffect(() => {
      getUsers().then(data => setUsers(data));
    }, []);
    ```
    With Convex Client, no state management needed and real time updates
    ```
    const users = useQuery(api.getUsers);
    ```
4. Convex Provider: ConvexProvider is a React context provider that makes the convex client available to all child components.
It wraps your entire application so that any component can call Convex functions without manually passing the {convex} as a prop
    ```
    export function ConvexClientProvider({ children }: { children: React.ReactNode }) {
      return (
        <ConvexProvider client={convex}>
          {children}
        </ConvexProvider>
      );
    }
    ```
    because of convex provider, we don't need to do this:
    ```
    //every component that needs to use convex would have to receive it as a prop
    const convex = new ConvexReactClient(import.meta.env.VITE_CONVEX_URL);
    function ChildComponent({ convex }) {
      const fetchData = async () => {
        const result = await convex.query(api.getUsers);
        console.log(result);
      };
      return <button onClick={fetchData}>Fetch Users</button>;
    }

    function ParentComponent() {
      return <ChildComponent convex={convex} />;
    }
    ```