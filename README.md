# CAMU_script
Creates Add and Manages Users
#!/bin/bash

echo "                                __                              .___ 
  ___________   ____ _____ _/  |_  ____   _____    ____    __| _/ 
_/ ___\_  __ \_/ __ \\__  \\   __\/ __ \  \__  \  /    \  / __ |  
\  \___|  | \/\  ___/ / __ \|  | \  ___/   / __ \|   |  \/ /_/ |  
 \___  >__|    \___  >____  /__|  \___  > (____  /___|  /\____ |  
     \/            \/     \/          \/       \/     \/      \/  
  _____ _____    ____ _____     ____   ____
 /     \\__  \  /    \\__  \   / ___\_/ __ \
|  Y Y  \/ __ \|   |  \/ __ \_/ /_/  >  ___/
|__|_|  (____  /___|  (____  /\___  / \___  >
      \/     \/     \/     \//_____/      \/
 __ __  ______ ___________  ______
|  |  \/  ___// __ \_  __ \/  ___/
|  |  /\___ \\  ___/|  | \/\___ \
|____//____  >\___  >__|  /____  >                                 "



# Define color variables
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Function to display the menu
display_menu() {
    echo -e "${BLUE}WELCOME!!${NC}"
    echo -e "${YELLOW}DO YOU WANT TO CREATE, DELETE USERS, CREATE GROUPS, DELETE GROUPS, OR EXPIRE USER PASSWORDS?${NC}"
    echo -e "${GREEN}CREATE USERS[1] DELETE USERS[2] CREATE GROUPS[3] DELETE GROUPS[4] EXPIRE USER PASSWORDS[5]${NC}"
}
# Function to create users
create_users() {
    read -p "Enter usernames separated by spaces: " -a users
    for user in "${users[@]}"; do
        echo -e "${BLUE}Adding user: $user${NC}"
        sudo useradd $user
        echo "$user:kali12345" | sudo chpasswd
    done
    assign_groups
}

# Function to assign users to groups
assign_groups() {
    echo -e "${YELLOW}Do you want to assign users to specific groups? (yes/no)${NC}"
    read assign_groups
    if [[ "$assign_groups" == "yes" ]]; then
        echo -e "${YELLOW}Do you want to assign users to specific groups or assign all to a single group?${NC}"
        echo -e "${GREEN}SPECIFIC GROUPS[1] SINGLE GROUP[2]${NC}"
        read group_option
        if [[ "$group_option" == "1" ]]; then
            for user in "${users[@]}"; do
                read -p "Enter the group for user $user: " group
                sudo groupadd $group 2>/dev/null
                sudo usermod -aG $group $user
                echo -e "${BLUE}Assigned user $user to group $group${NC}"
            done
        elif [[ "$group_option" == "2" ]]; then
            read -p "Enter the single group name: " single_group
            sudo groupadd $single_group 2>/dev/null
            for user in "${users[@]}"; do
                sudo usermod -aG $single_group $user
                echo -e "${BLUE}Assigned user $user to group $single_group${NC}"
            done        elif [[ "$group_option" == "2" ]]; then
            read -p "Enter the single group name: " single_group
            sudo groupadd $single_group 2>/dev/null
            for user in "${users[@]}"; do
                sudo usermod -aG $single_group $user
                echo -e "${BLUE}Assigned user $user to group $single_group${NC}"
            done
        else
            echo -e "${RED}Invalid option for group assignment.${NC}"
        fi
    else
        echo -e "${YELLOW}No group assignments made.${NC}"
    fi
}

# Function to delete users
delete_users() {
    read -p "Enter usernames to delete, separated by spaces: " -a users
    for user in "${users[@]}"; do
        echo -e "${BLUE}Deleting user: $user${NC}"
        sudo pkill -u $user 2>/dev/null # Kill all processes owned by the user
        sudo userdel -r $user 2>/dev/null
        if [[ $? -eq 0 ]]; then
            echo -e "${GREEN}User $user deleted successfully.${NC}"
            if getent group $user > /dev/null 2>&1; then
                sudo groupdel $user
                echo -e "${GREEN}Primary group $user deleted successfully.${NC}"
            fi
        else
            echo -e "${RED}Failed to delete user $user. Please check if the user exists.${NC}"
        fi
    done
}
# Function to create groups
create_groups() {
    read -p "Enter group names separated by spaces: " -a groups
    for group in "${groups[@]}"; do
        echo -e "${BLUE}Creating group: $group${NC}"
        sudo groupadd $group
    done
}

# Function to delete groups
delete_groups() {
    read -p "Enter group names to delete, separated by spaces: " -a groups
    for group in "${groups[@]}"; do
        echo -e "${BLUE}Deleting group: $group${NC}"
        sudo groupdel $group
        if [[ $? -eq 0 ]]; then
            echo -e "${GREEN}Group $group deleted successfully.${NC}"
        else
            echo -e "${RED}Failed to delete group $group. Please check if the group exists.${NC}"
        fi
    done
}

# Function to expire user passwords
expire_user_passwords() {
    read -p "Enter the username to expire the password: " user
    if id "$user" &>/dev/null; then
        sudo passwd -d $user
        sudo passwd -e $user
        echo -e "${GREEN}Password for user $user has been deleted and expired. They will need to reset it on next login.${N>
    else
        echo -e "${RED}User $user does not exist. Would you like to create a new user? (yes/no)${NC}"
        read create_userif [[ "$create_user" == "yes" ]]; then
            sudo useradd $user
            echo "$user:kali12345" | sudo chpasswd
            echo -e "${GREEN}User $user created successfully.${NC}"
        else
            echo -e "${YELLOW}No user created.${NC}"
        fi
    fi
}

# Main script loop
while true; do
    display_menu
    read -p "Enter an option: " output
    uppercase_output="${output^^}"
    case "$uppercase_output" in
        1 | "CREATE USERS")
            create_users
            ;;
        2 | "DELETE USERS")
            delete_users
            ;;
        3 | "CREATE GROUPS")
            create_groups
            ;;
        4 | "DELETE GROUPS")
            delete_groups
            ;;
        5 | "EXPIRE USER PASSWORDS")
            expire_user_passwords
            ;;        *)
            echo -e "${RED}Invalid option selected. Please try again.${NC}"
            ;;
    esac
done



            


        

