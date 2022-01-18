![image](https://user-images.githubusercontent.com/93277335/149855747-fc0cef08-c627-4d73-832a-1227557e0832.png)

# Presentation
Here is the presentation posted to my YouTube page</br>
https://www.youtube.com/watch?v=qIX37TGqIA4&ab_channel=frostbyte000jm

## Team
### Project Manager
Evelyn Vaughn</br>

### Front-end Developers
Steven Wallace</br>
Nick Tibbetts</br>
Jorge Caballero</br>
James Martin</br>

### Back-end Developers
Peter Doro</br>
Khanh Ngyen</br>
Evelyn Vaughn</br>
James Martin</br>


# Problem
A local physician is hiring our team to create a Telemedicine Application. They are looking for a web portal to communicate better with their patients. 

The Portal must allow the patients to send and receive messages, view reports, make appointments, and host video chats. 


# My Part of the Project
I was responsible for the Reports page, the Doctor Admin Portal, and all of the objects used. 

## Objects
Here is a sample of the objects created for this project

### Button
![image](https://user-images.githubusercontent.com/93277335/149675331-99e0c469-1bce-4db2-bd0a-f83b8f5c3f57.png)</br>
Each of these buttons would link in a new window or send to a new page

Object Link (for Buttons)
```js
import React from 'react'
import PropTypes from 'prop-types'
import ObjButton from './ObjButton'
import { Link } from 'react-router-dom';

const ObjLink = ({ linkInfo, text, onClick, doNewWindow, doLink, data, btnHeight, btnWidth}) => {
    
    // Should this button Link ("true"), or send to the same page ("false").
    doLink = (doLink === "true");
    doNewWindow = (doNewWindow === "true");
    const doBtnHeight = (btnHeight != null);
    const doBtnWidth = (btnWidth != null);
    //console.log("doLink:",doLink,"doNewWindow",doNewWindow, "doBtnHeight:", doBtnHeight, "doBtnWidth:", doBtnWidth);
    
    
    // Turn data into a string to pass to another page (see report.js to reportDisplay.js for structure)
    const dataString = JSON.stringify(data);    
    //console.log("dataString", dataString);
    
    if (doLink) {
        return (
            <Link
                to = {{
                    pathname: `${linkInfo}`,
                    state: { data: `${dataString}`},
                }}
                target={doNewWindow ? '_blank' : '_self'}
            >
                <ObjButton
                    text={text}
                    onClick={onClick}
                    nHeight={btnHeight}
                    nWidth={btnWidth}
                />
            </Link>
        )
    } else {
        return(
            <ObjButton
                text={text}
                onClick={onClick}
                nHeight={btnHeight}
                nWidth={btnWidth}
            />
        )
    }
}

ObjLink.defaultProps = {
    doNewWindow: "false",
    doLink: "true",
}

ObjLink.propTypes = {
    linkInfo: PropTypes.string,     //This should be a direct to a page in the project
    text: PropTypes.string,         //This is what the button should read
    onClick: PropTypes.func,        //Function for onClick (if used)
    doNewWindow: PropTypes.string,  //If you want this to open in a new window
    doLink: PropTypes.string,       //If you want the button to link
    data: PropTypes.object,         //On Link, what data do you wish to pass along.
    btnHeight: PropTypes.string,    //To set a Height for Button
    btnWidth: PropTypes.string,     //To set a Width for Button
    sPosition: PropTypes.string,     //To set a Width for Button
    sLeft: PropTypes.string,     //To set a Width for Button
    sRight: PropTypes.string,     //To set a Width for Button
    sTop: PropTypes.string,     //To set a Width for Button
    sBottom: PropTypes.string,     //To set a Width for Button

}

export default ObjLink
```

## Popup Windows
I realized early on that everyone was creating their own popup window and none of the windows were being built the same, therefore, I created a model template that everyone could use.

```js
import React from 'react'
import PropTypes from 'prop-types'

/* make sure you define props.header before calling this. 
    on creation between the two popup tags, the rest of the HTML will flow in where the props.children live.
*/
const OBJPopUpInput = ( props ) => {
    return ( props.trigger ) ? (
        <div className="objpopup-popup">            
            <div className="objpopup-popup-inner">
                
            
                <button 
                    type='button'
                    className="close-btn" 
                    class="close"
                    onClick={()=> props.setTrigger(false)}
                >
                   
                </button>
                <h1>{props.header}</h1>
                { props.children }
                
            </div>            
        </div>
    ) : "";
}

OBJPopUpInput.propTypes = {

}

export default OBJPopUpInput
```

Here is what a small sample looked like when using PopUpWindow
```js
            <PopUpWindow
                    trigger = {trigger}
                    setTrigger = {setTrigger}
                    header = "Add Notes"
            >                
                <textarea
                    defaultValue={textInput}
                    style={{width:"500px"}}
                    type="text"    
                    onChange={e=>onTextChange(e)}   
                />
                <ObjButton                     
                    text="Submit"
                    onClick={e=>onSubmit(e)}
                />
            </PopUpWindow>

```

## Axios Get and Post
I wrote the standards for our team to use when calling the backend. 

```js
 /***************************************************** 
                    Axios Get
    ******************************************************/
    const getListDaysOff = (  ) => {
        //console.log("getListDaysOff() - starting");
        let data = [];
        Axios.get(`https://telemedicine5a-backend.herokuapp.com/daysOff/getDaysOff/${authUserObject.userId}`)
            .then((response) => {   
                //console.log("getListDaysOff() - response:",response);             
                data = response.data[0];           
                //console.log("getListDaysOff data:",data);
                if(data){
                    let arrDaysGet = data.daysOff.split("|");
                    setBoxDaysOff(arrDaysGet);
                }
            }).catch((err) => {
                console.log(err, "Unable to get doctors/getDoctorInfo");
            });
    }

    const setListDaysOff = ( txtDays ) => {
        //console.log("checkDaysOff() - starting");
        let data = [];
        Axios.get(`https://telemedicine5a-backend.herokuapp.com/daysOff/getDaysOff/${authUserObject.userId}`)
            .then((response) => {   
                //console.log("checkDaysOff() - response:",response);             
                data = response.data;           
                //console.log("checkDaysOff data:",data);
                if (data.length > 0){
                    updateDaysOff(txtDays);
                } else {
                    newDaysOff(txtDays);
                }
            }).catch((err) => {
                console.log(err, "Unable to get doctors/getDoctorInfo");
            });
    }

    /***************************************************** 
                    Axios Post
    ******************************************************/
    const updateDaysOff = (txtDays) => {
        //console.log("updateDaysOff() - txtDays:",txtDays );
        Axios.post(`https://telemedicine5a-backend.herokuapp.com/daysOff/updateDaysOff/${authUserObject.userId}`, {
            daysOff:    txtDays,
        }).then(response => {
            console.log(response)
        }).catch((err) => {
            console.log(err)
        });
    }

    const newDaysOff = (txtDays) => {
        //console.log("newDaysOff() - txtDays:",txtDays );
        Axios.post(`https://telemedicine5a-backend.herokuapp.com/daysOff/addDaysOff`, {
            doctorUID:  authUserObject.userId,
            daysOff:    txtDays,
        }).then(response => {
            //console.log(response)
        }).catch((err) => {
            console.log(err)
        });
    }
```
