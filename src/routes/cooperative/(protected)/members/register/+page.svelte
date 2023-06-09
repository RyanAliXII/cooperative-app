
<script lang="ts">
  import SelectField from "$lib/components/form/SelectField.svelte";
  import TextAreaField from "$lib/components/form/TextAreaField.svelte";
  import TextField from "$lib/components/form/TextField.svelte";
  import {createForm} from "felte"
  import { validator } from '@felte/validator-yup';
  import { NewMemberValidationSchema } from "$lib/definitions/schema";
  import axios from "axios";
  import toast,{ Toaster} from "svelte-french-toast";

  type Dependent = {
    name: string,
    relationship:string,
    birthday: string
  }


  type Member = {
    givenName: string
    middleName: string,
    surname: string
    birthday: string,
    educationalAttainment: string,
    TIN: string,
    spouseName:string,
    civilStatus: string
    presentAddress: string,
    provincialAddress:string
    officeAddress: string,
    email:string
    mobileNumber:string
    officePhoneNumber:string
    dependents: Dependent[]

  }
  
  const {form, data, errors} = createForm<Member>({
    initialValues:{
        dependents:[]
    },
    extend: [validator({schema: NewMemberValidationSchema, castValues:  true })],
    onSubmit:async(body)=>{
      try{
        const response  = await axios.post("/api/members", body, {
          withCredentials: true
        })
        console.log(response.data)
        toast.success("Member has been registered.")
      }
      catch(error){
          console.log(error)
          toast.error("Unknown error occured.")
      }
    
    }
  })

  const addDependent = ()=>{
    const newDependent =  {name:"", relationship:"", birthday: ""}
    data.update(prev=>{
      prev.dependents.push(newDependent)
      return prev
    })
  }
  const removeDependent = (index: number)=>{
    data.update(prev=>{
      prev.dependents = prev.dependents.filter((_, i)=>i != index)
      return prev
    })
  }

</script>
<div>
  
    <h1 class="text-lg font-semibold mb-3 text-gray-500">Register Member</h1>
    <div class="container bg-base-100 w-full  p-3 rounded">
      <form use:form>
        <div>
            <div class="w-full h-10 rounded flex items-center px-2 text-gray-600 font-semibold gap-2">
                <i class="fa-regular fa-address-card"></i> MEMBER BASIC INFO
            </div>
            <div class="grid grid-cols-1 gap-2 md:grid-cols-3">
                <TextField  label="Given name" labelFor="givenName"  name="givenName" error={$errors?.givenName?.[0]}/>
                <TextField   label="Middle name" labelFor="middleName"  name="middleName" error={$errors?.givenName?.[0]}/>
                <TextField   label="Surname" labelFor="surname"  name="surname" error={$errors?.surname?.[0]}/>
                <TextField   label="Date of Birth" labelFor="birthday"  name="birthday" type="date" error={$errors?.birthday?.[0]}/>
                <SelectField label="Educational Attainment" name="educationalAttainment" error={$errors?.educationalAttainment?.[0]}>
                  <option value="high-school-graduate">Highschool Graduate</option>
                  <option value="high-school-undergraduate">Highschool Undergraduate</option>
                  <option value="college-undergraduate">College Undergraduate</option>
                  <option value="college-graduate">College Graduate</option>
              </SelectField>
                <TextField   label="Tax Identification No." labelFor="TIN"  name="TIN" error={$errors?.TIN?.[0]}/>
                <SelectField label="Civil Status" name="civilStatus" error={$errors?.civilStatus?.[0]}>
                    <option value="single">Single</option>
                    <option value="married">Married</option>
                    <option value="divorced">Divorced</option>
                </SelectField>
                <TextField   label="Name of Spouse(If married)" labelFor="spouseName"  name="spouseName" error={$errors?.spouseName?.[0]}/>
            </div>
            <div class="bg-base-200 w-full h-10 rounded flex items-center px-2 text-gray-600 font-semibold gap-2 mt-5">
                <i class="fa-regular fa-address-card"></i>  ADDRESSES
            </div>
            <TextAreaField  label="Present Home/ Mailing Address" labelFor="presentAddress"  name="presentAddress" error={$errors?.presentAddress?.[0]}/>
            <TextAreaField  label="Provincial Address" labelFor="provincialAddress"  name="provincialAddress" error={$errors?.provincialAddress?.[0]}/>
            <TextAreaField  label="Office Address" labelFor="officeAddress"  name="officeAddress" error={$errors?.officeAddress?.[0]}/>
            <div class="grid grid-cols-1 gap-2 md:grid-cols-2">
              
            </div>
            <div class="bg-base-200 w-full h-10 rounded flex items-center px-2 text-gray-600 font-semibold gap-2 mt-5">
                <i class="fa-regular fa-address-card"></i>  CONTACT INFO
            </div>
            <div class="grid grid-cols-1 gap-2 md:grid-cols-3">
                <TextField  label="Email" labelFor="email"  name="email" type="email" error={$errors?.email?.[0]}/>
                <TextField   label="Phone number" labelFor="mobileNumber"  name="mobileNumber" error={$errors?.mobileNumber?.[0]} />
                <TextField   label="Office Phone Number" labelFor="officePhoneNumber"  name="officePhoneNumber" error={$errors?.officePhoneNumber?.[0]}/>
              
            </div>
            <div class="bg-base-200 w-full h-10 rounded flex items-center px-2 text-gray-600 font-semibold gap-2 mt-5">
                <i class="fa-regular fa-address-card"></i>  DEPENDENTS
            </div>
            <button class="btn btn-outline btn-primary mt-5" type="button" on:click={addDependent}>Add Dependent</button>
            <div class="overflow-x-auto mt-5">
                <table class="table w-full">
                  <!-- head -->
                  <thead>
                    <tr>
                      <th>Name of Dependent/s</th>
                      <th>Relationship</th>
                      <th>Date of Birth</th>
                      <th></th>
                    </tr>
                  </thead>
                  <tbody>
                    {#each $data?.dependents as _, i }
                    <tr>
                      <td>
                        <TextField placeholder="Dependent name" name="" bind:value={$data.dependents[i].name} type="text"  error={$errors?.dependents?.[i]?.name?.[0]} noErrorText={true}/>
                        <small class="text-error ml-1 mt-1"> {$errors?.dependents?.[i]?.name?.[0] ?? ""}</small>
                      </td>

                      <td>
                        <SelectField name="" placeholder="Your relationship with dependent" bind:value={$data.dependents[i].relationship} error={$errors?.dependents?.[i]?.relationship?.[0]} noErrorText={true}>
                        <option value="father">Father</option>
                        <option value="mother">Mother</option>
                        <option value="spouse">Spouse</option>
                        <option value="children">Children</option>
                        </SelectField>
                        <small class="text-error ml-1 mt-1"> {$errors?.dependents?.[i]?.relationship?.[0] ?? ""}</small>
                      </td>
                      
                      <td>
                        <TextField name=""  bind:value={$data.dependents[i].birthday} type="date" error={$errors?.dependents?.[i]?.birthday?.[0]} noErrorText={true}/>
                        <small class="text-error ml-1 mt-1"> {$errors?.dependents?.[i]?.relationship?.[0] ?? ""}</small>
                      </td>
                      <td>
                        <button type="button" class="btn btn-error btn-outline mb-2" on:click={()=>{removeDependent(i)}}><i class="fa-solid fa-xmark"></i></button>
                 
                      </td>
                    </tr>
                    {/each}
                  
                  
                  </tbody>
                </table>
              </div>
          </div>
        <button class="btn btn-primary mt-10" type="submit">Register Member</button>
      </form>
      </div>
      <Toaster/>
</div>